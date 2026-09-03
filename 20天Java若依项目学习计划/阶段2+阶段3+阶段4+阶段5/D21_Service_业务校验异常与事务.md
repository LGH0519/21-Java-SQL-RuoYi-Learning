# D21｜Service、业务校验、统一异常与事务

> 今日目标：区分 Controller/Service/Mapper 职责，观察若依字典类型的唯一性校验与异常消息，不把规则只写在前端。

## 1. 知识速记

- Controller：HTTP 入口、参数/响应；Service：业务规则与多步骤协调；Mapper：数据库访问。
- 唯一性规则必须在后端校验，前端校验只能改善体验，不能防接口绕过。
- `@Transactional` 用在要么全成功、要么全失败的业务方法。
- 异常应由统一处理转换为清晰的失败响应，而不是吞掉或直接返回成功。

## 2. 资料

- `...\SysDictTypeController.java`：`add`、`edit`。
- `...\ruoyi-system\src\main\java\com\ruoyi\system\service\impl\SysDictTypeServiceImpl.java`：`checkDictTypeUnique`、`insertDictType`、`updateDictType`。
- `E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-common\src\main\java\com\ruoyi\common\exception\ServiceException.java`。

## 3. 知识详解

“字典类型不可重复”是业务规则，Service 查询后得出是否重复；Controller 根据结果拒绝请求；Mapper 只负责查询/写入。数据库唯一约束是最后防线，Service 给用户更易懂的错误。事务不是每个方法都要标：只有同一个业务动作包含多个必须一致的写操作时才需要。

## 4. 今天照做

### 09:00–12:00｜沿新增链读代码

1. 在 IDEA 依次打开 Controller 的 `add`、Service 的 `checkDictTypeUnique`、`insertDictType`。
2. 对每个方法填表：输入是什么、判断什么、调用谁、成功返回什么、失败如何表现。
3. 打开 `updateDictType`，找到 `@Transactional`（若版本/位置不同以实际为准），写其涉及的多项更新目标。

### 15:00–18:00｜用临时数据观察唯一性校验

1. 在字典管理页面点击新增，创建一个命名清晰的临时类型，例如 `learn_temp_type_你的日期`；保存成功。
2. 再用完全相同的“字典类型”提交。预期出现业务错误，不应新增第二条。
3. 在 `checkDictTypeUnique` 与 Controller `add` 打断点，Debug 再提交一次；观察唯一性查询结果和最终响应消息。
4. 测试结束，在页面删除这条临时字典（先确认名称完全一致）；刷新页面确认不在列表中。

### 19:00–23:00｜事务关系图与排错卡

1. 画“改字典类型 → 关联字典数据同步 → 字典类型本身更新”的关系图，标出它们为何需要一同成功。
2. 写档案项目类比：批准借阅会更新申请、写借阅记录、更新档案状态，因此 D38 需要事务。
3. 记录一次失败的 Response、后端日志位置、DBeaver 验证结果；不要因演练修改全局异常处理器。

## 5. AI Agent 操作卡

要求 AI 评审一个 Service 方法时，先输出“规则、失败分支、事务边界、验证用例”四栏；确认后才允许它提出最小代码改动。避免“把所有异常 catch 后返回成功”的建议。

## 6. 验收与文末答案

- [ ] 能区分三层职责。
- [ ] 实测并删除了临时重复字典数据。
- [ ] 画出一个需要事务的多表状态关系。

答案：重复判断只放前端为什么不够？可绕过页面直接请求接口；后端必须执行业务规则，数据库唯一约束再做最终保护。
