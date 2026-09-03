# D01 今天照做执行清单：项目生命周期与 AI 协作

> 目标：今天不改项目代码。完成项目地图、AI 协作卡和一次前后端启动/Network 证据。

## 开始前（09:00–09:20）

- [ ] 打开文件资源管理器，进入 `E:\WorkSpace\21-Java-SQL-RuoYi-Learning\20天Java若依项目学习计划\阶段0__确认学习计划\V2_AI协作档案系统学习手册`。
- [ ] 右键空白处 → **新建 → 文本文档**；改名为 `D01-notes.md`。如果 Windows 隐藏扩展名，文件可能显示为 `D01-notes.md.txt`；在资源管理器 **查看 → 显示 → 文件扩展名** 勾选后再改名。
- [ ] 用记事本或 IDEA 打开它，粘贴以下标题：

```markdown
# D01 记录
## 项目资产
## AI 审查表
## 启动证据
## 调试记录
```

- [ ] 今天不打开或编辑 `application.yml`、`pom.xml`、`vite.config.js` 的保存功能；只读。

## 上午：确认项目资产（09:20–12:00）

1. [ ] 打开 IDEA 欢迎页或菜单 **File → Open**。
2. [ ] 输入/选择目录：`E:\WorkSpace\ruoyi-archive-management\backend`，点击 **OK**。
3. [ ] 等待右下角 Maven 索引完成；不要点击“升级依赖”提示。
4. [ ] 在左侧 Project 中展开：`ruoyi-admin → src → main → java → com → ruoyi`。
5. [ ] 双击 `RuoYiApplication.java`；在 `D01-notes.md` 的“项目资产”下写：`后端启动类：RuoYiApplication.java`。
6. [ ] 按 **Ctrl+N**，输入 `pom.xml`，选择根目录的 `pom.xml`；按 **Ctrl+F** 搜索 `<java.version>`，把显示的版本写到笔记。
7. [ ] 在文件资源管理器打开 `E:\WorkSpace\ruoyi-archive-management\frontend`。
8. [ ] 右键 `package.json` → 用 IDEA/记事本打开；搜索 `"dev"`，把脚本内容写到笔记。
9. [ ] 打开 `vite.config.js`；按 **Ctrl+F** 依次搜索 `port:`、`/dev-api`、`proxy`。写下“前端端口”和“代理前缀”。不要复制 `target` 中任何敏感环境信息。
10. [ ] 在笔记画：`浏览器 → 前端 :80 → /dev-api → 后端 :8080 → MySQL/Redis → 返回 JSON`。

**上午验收：** 笔记中必须有 Java 版本、前端启动脚本、两个端口、代理前缀和一张箭头图。

## 下午：完成 AI 协作卡（15:00–18:00）

1. [ ] 在 `D01-notes.md` 新增 `## AI 审查表`。
2. [ ] 复制下方内容到 AI Agent 聊天框；不要附任何密码、Token、Cookie、数据库连接配置或真实附件：

```text
背景：我在 RuoYi-Vue 档案管理系统第一版做立项分析。
角色：普通员工、档案管理员、系统管理员。
第一版要做：分类、档案、附件、检索、申请/撤销、审批/驳回、借出、归还、逾期查询。
不做：多级审批、全文检索、跨部门调阅、标签设备、短信邮件。
请只输出：1) 需求复述；2) 角色职责表；3) 状态清单；4) 风险和待确认问题。
不要生成代码、SQL、配置或文件修改。
```

3. [ ] 阅读 AI 回复，在笔记建立 5 行表格：`需求复述是否正确 / 是否包含三角色 / 是否加入范围外功能 / 是否有待确认问题 / 是否泄露敏感信息`。
4. [ ] 若 AI 加入多级审批、短信、全文检索，在表格中写“拒绝：第一版不做”。
5. [ ] 将合格结论写为：`今天 AI 仅用于分析，未生成或修改任何代码。`

## 晚上：启动与浏览器证据（19:00–23:00）

1. [ ] 回到 IDEA 的 `RuoYiApplication.java`；点击类名左侧绿色三角 → 选择 **Run 'RuoYiApplication'**。
2. [ ] 在底部 **Run** 面板等待；用鼠标点入输出区后按 **Ctrl+F**，搜索 `Started RuoYiApplication`。
3. [ ] 再搜索 `若依启动成功`。两个都找到后截图，保存为 `D01-后端启动.png`；截图前确认没有密码。
4. [ ] 打开 Git Bash；输入：

```bash
cd /e/WorkSpace/ruoyi-archive-management/frontend
npm run dev
```

5. [ ] 等待终端显示 Local 地址；浏览器打开 `http://localhost/`。
6. [ ] 使用你已有本地测试账号登录；不要把账号密码写入笔记或截图。
7. [ ] 按 **F12** → 点击 **Network** → 勾选 **Preserve log** → 点击清空图标。
8. [ ] 菜单进入 **系统管理 → 字典管理 → 字典类型**；在过滤框输入 `type/list`。
9. [ ] 点击列表请求，记录 Request Method、Status Code、Response 中是否有 `rows`。截图前遮挡 Authorization/Cookie。
10. [ ] 在笔记“调试记录”写：现象“8080 只显示后端提示”；结论“管理页来自前端 :80”。

## 收尾验收

- [ ] `D01-notes.md` 四个标题均有内容。
- [ ] 有后端启动和脱敏 Network 两张截图。
- [ ] 能说清 AI 今天做了什么、没有做什么。
- [ ] 关闭不再使用的浏览器调试窗口；前后端可继续运行供明天使用。
