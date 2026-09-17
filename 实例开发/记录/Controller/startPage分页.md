这两个方法是若依对 PageHelper 分页插件的封装，但职责完全不同：

```
startPage()：控制数据库“查哪一页、查多少条”
getDataTable()：把查询结果包装成前端表格认识的响应格式
```

## 1. `startPage()` 实现了什么？

你 Controller 中的代码：

```
startPage();

List<BizRegistration> registrationList =
        bizRegistrationService.selectRegistrationListForManagement(registrationQuery);
```

`startPage()`主要完成三件事：

1. 从当前 HTTP 请求中读取分页参数。
2. 把分页参数交给 PageHelper。
3. PageHelper 拦截紧随其后的 MyBatis 查询，为 SQL 增加分页，并执行总数统计。

例如前端发出：

```
GET /registration/manage/list?pageNum=2&pageSize=10
```

原始 SQL 类似：

```
select *
from biz_registration
where registration_year = 2026
order by submit_time desc;
```

PageHelper 会参与处理，使数据库只返回第二页的数据，效果类似：

```
select *
from biz_registration
where registration_year = 2026
order by submit_time desc
limit 10, 10;
```

同时还需要获得符合条件的总记录数，供前端显示：

```
共 35 条
```

### 为什么 `startPage()` 后面必须紧跟查询？

PageHelper 会把分页信息暂存在当前请求线程中，然后拦截接下来的第一个 MyBatis 查询。

所以应该这样写：

```
startPage();

List<BizRegistration> registrationList =
        bizRegistrationService.selectRegistrationListForManagement(registrationQuery);
```

不要这样写：

```
startPage();

// 这个查询可能先消耗掉分页设置
List<SysDictData> dictList = sysDictService.selectDictDataByType("xxx");

// 真正想分页的查询反而可能不正确
List<BizRegistration> registrationList =
        bizRegistrationService.selectRegistrationListForManagement(registrationQuery);
```

---

## 2. `startPage()` 怎么从 HTTP 请求中获得参数？

虽然 `startPage()` 没有方法参数：

```
startPage();
```

但它可以通过 Spring 保存的“当前 HTTP 请求上下文”取得请求对象。

当前项目中的调用链是：

```
Controller.startPage()
        ↓
PageUtils.startPage()
        ↓
TableSupport.buildPageRequest()
        ↓
TableSupport.getPageDomain()
        ↓
ServletUtils.getParameter("pageNum")
        ↓
HttpServletRequest.getParameter("pageNum")
```

核心源码相当于：

```
pageDomain.setPageNum(
        Convert.toInt(ServletUtils.getParameter("pageNum"), 1)
);

pageDomain.setPageSize(
        Convert.toInt(ServletUtils.getParameter("pageSize"), 10)
);
```

其中：

```
ServletUtils.getParameter("pageNum")
```

最终执行的是：

```
getRequest().getParameter("pageNum");
```

而 `getRequest()` 又通过 Spring 的 `RequestContextHolder` 取得当前请求：

```
RequestContextHolder.getRequestAttributes();
```

所以：

```
GET /registration/manage/list?pageNum=2&pageSize=10
```

会得到：

```
pageNum = 2;
pageSize = 10;
```

如果前端没有传分页参数，若依使用默认值：

```
pageNum = 1;
pageSize = 10;
```

需要注意：这些是 URL 查询参数，不是 JSON 请求体。

---

## 3. `getDataTable()` 实现了什么？

数据库查询完成后，你得到的是：

```
List<BizRegistration> registrationList
```

它本质上只是“当前这一页的数据集合”，例如10条报名记录。

若依前端分页表格不仅需要这10条数据，还需要：

- 请求是否成功；
- 提示信息；
- 当前页的数据；
- 符合条件的总记录数。

因此 `getDataTable()` 将集合包装成 `TableDataInfo`：

```
protected TableDataInfo getDataTable(List<?> list)
{
    TableDataInfo rspData = new TableDataInfo();

    rspData.setCode(HttpStatus.SUCCESS);
    rspData.setMsg("查询成功");
    rspData.setRows(list);
    rspData.setTotal(new PageInfo(list).getTotal());

    return rspData;
}
```

最终返回的 JSON 类似：

```
{
  "code": 200,
  "msg": "查询成功",
  "rows": [
    {
      "registrationId": 1,
      "realName": "李小刚"
    }
  ],
  "total": 35
}
```

其中：

```
rows  = 当前页的记录
total = 所有符合查询条件的记录总数
```

前端分页组件需要 `total` 才能计算共有多少页。

---

## 4. 必须通过 `getDataTable()` 转换吗？

不必须。

`getDataTable()`不是 Java、Spring或MyBatis的强制要求，只是若依提供的方便方法。

### 分页列表：建议使用

对于管理员报名列表这种若依分页表格，建议写：

```
startPage();

List<BizRegistration> registrationList =
        bizRegistrationService.selectRegistrationListForManagement(registrationQuery);

return getDataTable(registrationList);
```

因为前端通常默认读取：

```
response.rows
response.total
```

### 普通列表：可以不使用

如果数据量很小且不分页，可以直接返回：

```
List<BizRegistration> registrationList =
        bizRegistrationService.selectRegistrationListForManagement(registrationQuery);

return success(registrationList);
```

响应会类似：

```
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "registrationId": 1
    }
  ]
}
```

这时前端要读取：

```
response.data
```

而不是：

```
response.rows
```

### 单条详情：不应该使用

报名详情只返回一个对象，应该写：

```
BizRegistration registration =
        bizRegistrationService.selectRegistrationDetailForManagement(
                registrationId,
                CURRENT_REGISTRATION_YEAR);

return success(registration);
```

不需要：

```
startPage();
getDataTable(...);
```

因为详情不是集合，也不存在分页。

## 最终记忆方式

```
查分页列表：
startPage()
→ Mapper查询
→ getDataTable(list)

查普通小列表：
Mapper查询
→ success(list)

查单条详情：
Mapper查询
→ success(object)
```

所以你当前的管理员列表用法是正确的：

```
startPage();
List<BizRegistration> registrationList = ...
return getDataTable(registrationList);
```

但之后的“报名详情接口”不需要这两个分页方法。