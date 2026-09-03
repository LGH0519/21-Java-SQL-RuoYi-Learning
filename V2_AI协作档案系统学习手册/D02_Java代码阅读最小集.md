# Day 02：Java 代码阅读最小集——变量、条件、循环、方法

## 知识速记

Java 阅读先找方法入口，再看变量、条件、循环和 `return`。`if` 决定走哪条分支，`for/while` 重复执行，方法参数是输入，`return` 是输出。比较字符串内容使用 `"文本".equals(value)`；`=` 是赋值，`==` 对基本类型比较值。

## 资料

- [Java 基础语法](https://www.runoob.com/java/java-basic-syntax.html)
- [Java 运算符](https://www.runoob.com/java/java-operators.html)
- [Java 条件语句](https://www.runoob.com/java/java-if-else-switch.html)
- [Java 循环](https://www.runoob.com/java/java-loop.html)
- [Java 方法](https://www.runoob.com/java/java-methods.html)

只看变量、`if/else`、`for`、`return`；跳过并发、网络、反射。

## 知识详解

### 1. 变量和类型

`String` 放文字；`int/long` 放整数；`boolean` 放真/假；`List<T>` 放多条同类型数据。变量名右侧的值在代码运行中可改变；`final` 表示变量不能再次赋值。

### 2. 条件

```java
if (archive == null) { return "未找到"; }
if (!archive.canBorrow()) { return "不可借"; }
return "可借";
```

代码从上到下判断；第一个 `return` 会立即结束当前方法。`&&` 要所有条件为真，`||` 只要一个为真，`!` 取反。条件应读成中文，不能只背符号。

### 3. 循环

```java
for (SysDictType item : list) { /* 每次处理一条 */ }
```

循环要明确“遍历谁、每次处理什么、何时结束”。数组用 `length`，List 用 `size()`；集合遍历优先读 `for-each`。

### 4. 方法

`public List<SysDictType> selectDictTypeList(SysDictType dictType)`：返回多条字典类型；方法名是 `selectDictTypeList`；参数是查询条件。读方法时先问：输入是什么？输出是什么？中间调用谁？失败怎样返回？

## 今天照做（09:00–23:00）

### 09:00–12:00：读资料并标注语法

1. 阅读上述资料，每个概念只写一个档案例子。
2. 打开 `SysDictTypeController.java`，找到 `list(SysDictType dictType)`。
3. 用颜色或笔记标注：参数 `dictType`、局部变量 `list`、方法调用、`return getDataTable(list)`。
4. 用中文逐行翻译，不理解的 Spring 注解先标“D03/D04 学”。

### 15:00–18:00：手工执行一次代码阅读

1. 打开 `SysDictTypeServiceImpl.selectDictTypeList`，回答其输入、输出、唯一核心动作。
2. 打开 `SysDictTypeMapper.java`，找同名方法，比较参数和返回类型是否一致。
3. 在纸上写出档案借阅伪代码：找档案 → 找不到返回失败 → 不可借返回失败 → 否则返回成功。不得让 AI 直接写 Java。
4. 用 AI 操作卡请它只审查伪代码的遗漏状态，不生成代码。

### 19:00–23:00：断点观察变量

1. 在 Controller 的 `List<SysDictType> list = ...` 行打断点，Debug 启动后端。
2. 浏览器打开字典类型列表；暂停后观察 `dictType` 和 `list`。
3. F7 进入 Service，F8 执行当前行，F9 放行；不要进入 Spring/MyBatis 内部。
4. 写调试记录：请求输入、Controller 输出、为什么 `return` 会回到浏览器。

## AI 操作卡

向 AI 提供一个方法，不超过 40 行，要求输出“输入/输出/分支/调用链/潜在空值”；然后自己逐项在源码验证。若 AI 给出不存在的文件或方法，记录为错误而非照做。

## 验收

- [ ] 能解释 `dictType`、`list` 和 `return getDataTable(list)`。
- [ ] Debug 中看到至少一个真实参数和返回列表。
- [ ] 能写出借阅伪代码的 3 个失败分支。

## 文末答案与自测

**`if (archive == null)` 表示什么？** 档案查询结果为空，不能继续调用它的方法。  
**`return` 后的语句会执行吗？** 当前方法不会。  
**通过标准：** 不看资料解释一个 Controller 方法的输入、输出、分支和下一层调用。
