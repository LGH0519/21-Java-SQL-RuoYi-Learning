# D02 今天照做执行清单：Java 代码阅读最小集

> 目标：不写最终项目代码；在真实字典类型方法上标出参数、条件、循环、返回值，并完成一次断点。

## 开始前（09:00–09:15）

- [ ] 打开 `D02_Java代码阅读最小集.md` 和新建 `D02-notes.md`。
- [ ] 在笔记输入四行：`输入：`、`局部变量：`、`分支：`、`输出：`。
- [ ] 保持 D01 后端和前端运行；若已停止，先按 D01 晚上步骤启动。

## 上午：阅读语法（09:15–12:00）

1. [ ] 在浏览器依次打开 D02 文档中的 Java 基础语法、条件、循环、方法链接。
2. [ ] 每个页面只记一条：`String`、`if`、`for`、`return`；不要在网页中继续点到并发/网络。
3. [ ] 在 IDEA 按 **Ctrl+N**，输入 `SysDictTypeController`，打开该类。
4. [ ] 按 **Ctrl+F** 输入 `public TableDataInfo list`，按 Enter 到列表方法。
5. [ ] 将下列四项复制到笔记并填右侧：

```text
方法参数：SysDictType dictType →
局部变量：List<SysDictType> list →
调用的方法：dictTypeService.selectDictTypeList(dictType) →
返回语句：return getDataTable(list) →
```

6. [ ] 用鼠标选中 `dictType`，按 **Ctrl+B** 或 **Ctrl+鼠标左键**，确认能跳转到类定义；按 **Alt+Left** 返回。

## 下午：手工追一层调用（15:00–18:00）

1. [ ] 选中 `selectDictTypeList`，按 **Ctrl+B**；若先跳到接口，记录“接口声明”，再用 **Ctrl+Alt+B** 找实现。
2. [ ] 打开 `SysDictTypeServiceImpl` 的同名方法；在笔记填：输入、输出、唯一执行语句。
3. [ ] 用自己的话写 4 行借阅伪代码：`查档案`、`找不到`、`不可借`、`成功`。
4. [ ] 把伪代码发给 AI，输入：`只找遗漏失败状态，不写 Java、SQL 或修改建议。`。
5. [ ] 把 AI 的建议分为“接受/拒绝/待确认”，并写理由。

## 晚上：Debug（19:00–23:00）

1. [ ] 在 Controller 的 `List<SysDictType> list = ...` 行号左侧单击，出现红点。
2. [ ] 停止普通 Run（底部红色方块）；点击绿色小虫或按 **Shift+F9** Debug 启动后端。
3. [ ] 等启动成功后，浏览器刷新字典类型列表。
4. [ ] IDEA 暂停时，打开底部 **Debug** → **Variables**；展开 `dictType`，写下至少一个字段值。
5. [ ] 按 **F7** 一次，确认进入 Service；不要连续 F7。
6. [ ] 按 **F8**，观察当前行位置；再按 **F9** 放行。
7. [ ] 回浏览器，确认页面仍显示；点击红点移除断点。

## 收尾验收

- [ ] 笔记有方法输入、变量、调用、输出。
- [ ] 有一次 Controller→Service 断点截图。
- [ ] 能读出 `return` 的实际输出，不把它误认为打印日志。
