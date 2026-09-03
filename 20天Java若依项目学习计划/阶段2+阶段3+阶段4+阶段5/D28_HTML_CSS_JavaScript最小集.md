# D28｜HTML、CSS、JavaScript 最小集

> 今日目标：前端只为后端项目服务。能区分 HTML 结构、CSS 样式、JS 行为，并做一个不接接口的静态档案录入页。

## 1. 知识速记

- HTML 定义结构：标题、表单、输入框、表格、按钮。
- CSS 定义呈现：选择器、class、盒模型、间距、颜色。
- JavaScript 定义行为：变量、对象、数组、函数、事件。
- 浏览器 F12 的 Elements 看结构，Styles 看样式，Console 看 JS 输出/错误。

## 2. 资料

- [HTML 教程](https://www.runoob.com/html/html-tutorial.html)：重点 `form/input/select/table/button`。
- [CSS 教程](https://www.runoob.com/css/css-tutorial.html)：重点 class、盒模型、margin/padding。
- [JavaScript 教程](https://www.runoob.com/js/js-tutorial.html)：重点变量、对象、数组、函数。

## 3. 知识详解

HTML 的 `<input>` 不会自动保存到数据库；JS 的数组也不是 MySQL 表。页面点击后要经过 Vue/API/后端才会持久化，D30 再学习。`class` 可被多个元素复用，`id` 在同一页面应唯一。易混点：CSS 的 margin 是元素外侧间距，padding 是内容到边框内侧间距。

## 4. 今天照做

### 09:00–12:00｜创建隔离练习页

1. 在个人学习目录新建 `frontend-learning\day28\archive-form.html`，不放进若依 `frontend/src`。
2. 粘贴并保存：

```html
<!doctype html><html lang="zh-CN"><head><meta charset="UTF-8"><title>档案录入练习</title>
<style>.panel{max-width:620px;margin:30px auto;padding:20px;border:1px solid #ddd}.row{margin:12px 0}.row label{display:inline-block;width:110px}</style>
</head><body><main class="panel"><h1>档案录入（静态练习）</h1>
<div class="row"><label>档案编号</label><input id="archiveNo" placeholder="ARC-2026-001"></div>
<div class="row"><label>档案名称</label><input id="archiveName"></div>
<div class="row"><label>分类</label><select id="category"><option>合同档案</option><option>项目资料</option></select></div>
<div class="row"><label>保管年限</label><input id="years" type="number"></div><button id="saveBtn">保存（仅控制台）</button></main>
<script>document.querySelector('#saveBtn').addEventListener('click',()=>{const archive={archiveNo:archiveNo.value,archiveName:archiveName.value,category:category.value,years:Number(years.value)};console.log('当前档案对象：',archive);});</script>
</body></html>
```

3. 右键文件→用浏览器打开，确认四项表单和按钮出现。

### 15:00–18:00｜F12 看结构、样式与行为

1. F12 → Elements，点选 `<input>`，看 DOM；在 Styles 修改 `.panel` 的 padding 为 `40px`，只观察效果后刷新恢复。
2. 打开 Console，输入 `document.querySelector('#archiveName').value`，理解它读取输入值。
3. 填表单点击按钮，确认 Console 输出一个对象；这不是数据库写入。

### 19:00–23:00｜数组练习与验收

1. Console 输入：`const archives=[{archiveNo:'ARC-1',name:'合同'},{archiveNo:'ARC-2',name:'项目资料'}]; console.table(archives);`
2. 写一句解释：对象是一条档案的属性集合；数组是一组对象；函数是可反复调用的行为。
3. 截图页面、Elements、Console 三处，保存到 `day-28`。

## 5. AI Agent 操作卡

让 AI 只检查这一个静态 HTML 是否存在标签遗漏、label/输入框关联、明显 CSS 问题；不要让它把练习页直接合并进若依或引入新框架。

## 6. 验收与文末答案

- [ ] 独立静态页可打开并打印对象。
- [ ] 能指出 HTML/CSS/JS 各负责什么。
- [ ] 能用 F12 查看结构和 Console。

答案：点击“保存”是否已经入库？否，当前仅 JS 输出；没有 API/后端/MySQL 调用。
