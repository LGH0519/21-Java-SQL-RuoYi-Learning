# D03 今天照做执行清单：对象、集合、异常、注解

## 开始前（09:00–09:15）

- [ ] 新建 `D03-notes.md`，写标题：`对象`、`集合`、`异常`、`注解`、`JSON 对照`。
- [ ] 不编辑任何 Java 文件；今天只读和断点。

## 上午：对象与集合（09:15–12:00）

1. [ ] 在 IDEA 用 **Ctrl+N** 搜索 `SysDictType`，打开实体类。
2. [ ] 在“对象”标题下写 3 个字段名及中文含义；写下它是否 `extends BaseEntity`。
3. [ ] Ctrl+点击 `BaseEntity`；只找到 `createBy/createTime/updateBy/updateTime/remark`，写“公共字段”。
4. [ ] 回 Controller，搜索 `List<SysDictType> list`；写“List 中的一项是什么”。
5. [ ] 打开 Java 集合资料，只看 List/Set/Map 对比；写一条档案用途：列表、去重、编号查找。

## 下午：注解与异常（15:00–18:00）

1. [ ] 回 Controller 类头，逐个找到 `@RestController`、`@RequestMapping`。
2. [ ] 在 `list` 方法上找到 `@GetMapping`、`@PreAuthorize`。
3. [ ] 在笔记做四列表：注解、标注位置、今天可理解作用、暂不深入内容。
4. [ ] Ctrl+N 搜 `ServiceException`；打开后仅写“它继承 RuntimeException，用于业务层失败”。
5. [ ] 让 AI 解释一个实体类片段；要求固定输出“字段/继承/不确定点”，再逐项在源码核对。

## 晚上：对象与 JSON 对照（19:00–23:00）

1. [ ] 在 Controller `return getDataTable(list)` 前一行打断点并 Debug 后端。
2. [ ] 刷新字典类型页面；暂停后在 Variables 展开 `list` → 第一项。
3. [ ] 记录三个对象字段名和值；按 F9 放行。
4. [ ] F12 → Network → `type/list` → Response，找到 `rows[0]`。
5. [ ] 对照 Java 对象与 JSON 至少 3 个同名字段；截图时遮挡敏感信息。

## 收尾验收

- [ ] 能说出类、对象、List、rows 的关系。
- [ ] 四个注解已定位到真实位置。
- [ ] 有对象字段与 JSON 字段对照表。
