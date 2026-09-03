# Day 03：Java 对象、集合、异常与注解

## 知识速记

类是模板，对象是具体数据；字段保存状态，方法描述行为。`List<SysDictType>` 是多条字典类型；泛型 `<SysDictType>` 限制元素类型。异常表示不能按约定继续执行；注解 `@` 是给框架的标记，不是业务代码本身。

## 资料

- [Java 对象和类](https://www.runoob.com/java/java-object-classes.html)
- [Java 集合框架](https://www.runoob.com/java/java-collections.html)
- [Java 异常处理](https://www.runoob.com/java/java-exceptions.html)
- 本机：`SysDictType.java`、`BaseEntity.java`、`ServiceException.java`、`SysDictTypeController.java`。

## 知识详解

### 对象与封装

`SysDictType` 是“字典类型数据”的类；一次查询返回许多个对象。字段通常 `private`，用 getter/setter 读取或修改，目的是保证修改入口集中。继承 `extends BaseEntity` 表示它复用了创建人、创建时间、备注等公共字段；今天只读，不必手写继承。

### 集合

`List` 有顺序可重复，适合页面列表；`Set` 主要去重；`Map<K,V>` 用键找值。阅读 `List<SysDictType>` 时要说清“List 里每一项是 SysDictType，不是一列 SQL 字符串”。

### 异常与注解

`throw` 抛出问题，`try/catch` 在调用边界处理。业务中“正常但不可借”通常是业务结果；“参数根本非法”才可能抛异常。`@RestController` 标记 HTTP 控制器；`@GetMapping` 标记 GET 路径；`@PreAuthorize` 标记权限要求。

## 今天照做（09:00–23:00）

### 09:00–12:00

1. 阅读资料，写出对象/类、List、异常、注解四句定义。
2. 打开 `backend/ruoyi-common/src/main/java/com/ruoyi/common/core/domain/entity/SysDictType.java`，列出它的 3 个字段和继承关系。
3. 打开 `BaseEntity.java`，只看公共创建/更新时间字段；不研究序列化。

### 15:00–18:00

1. 在 `SysDictTypeController.list` 找 `List<SysDictType> list`，写明每层数据类型：页面数组、JSON rows、Java List、单个 SysDictType、数据库一行。
2. 打开 `ServiceException.java`，只记录它是运行时业务异常，不复制构造代码。
3. 在 Controller 找 `@RestController`、`@RequestMapping`、`@GetMapping`、`@PreAuthorize`，写“位置—作用—今天不深入的部分”。

### 19:00–23:00

1. 在 Controller 断点处展开 `list` 的一项，再展开对象字段；截图脱敏。
2. 在浏览器 Network 观察响应 `rows[0]` 的字段，和 Java 对象对比。
3. 向 AI 给出一个 `SysDictType` 类片段，要求它列出字段、继承、可能的空值；逐项在源码核对。

## AI 操作卡

要求 AI “解释，不改代码”；禁止让 AI 根据一个实体类臆造数据库字段。回答必须标出“从代码可知”和“需要查表/Mapper 才能确认”的界线。

## 验收

- [ ] 能区分类、对象、List 和 JSON rows。
- [ ] 能说出 4 个注解各自标在类还是方法上。
- [ ] 有一次对象字段与 Network JSON 的对照记录。

## 文末答案与自测

**List 与数组的关键差别？** List 长度可增减并提供集合操作；数组长度固定。  
**异常一定等于系统崩溃吗？** 不一定；可被统一处理并变成错误响应。  
**通过标准：** 看见 `List<SysDictType>` 能完整说明里面有什么、从哪来、返回到哪去。
