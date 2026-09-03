# `String` 类常用方法清单

`String` 是 Java 中最常用的类之一，表示**不可变的字符序列**（一旦创建就不能修改）。以下是开发中最常使用的方法：

---

## 一、获取信息类

| 方法 | 作用 | 示例 | 结果 |
|------|------|------|------|
| `length()` | 返回字符串长度 | `"Hello".length()` | `5` |
| `isEmpty()` | 判断是否为空字符串（长度为 0） | `"".isEmpty()` | `true` |
| `charAt(int index)` | 返回指定位置的字符 | `"Hello".charAt(1)` | `'e'` |
| `indexOf(String str)` | 返回子串第一次出现的位置，找不到返回 -1 | `"Hello World".indexOf("World")` | `6` |
| `lastIndexOf(String str)` | 返回子串最后一次出现的位置 | `"abcabc".lastIndexOf("abc")` | `3` |
| `contains(CharSequence s)` | 判断是否包含某个子串 | `"Hello".contains("ell")` | `true` |
| `startsWith(String prefix)` | 判断是否以某前缀开头 | `"Hello".startsWith("He")` | `true` |
| `endsWith(String suffix)` | 判断是否以某后缀结尾 | `"Hello.java".endsWith(".java")` | `true` |

---

## 二、截取与分割类

| 方法 | 作用 | 示例 | 结果 |
|------|------|------|------|
| `substring(int beginIndex)` | 从指定位置截取到末尾 | `"Hello".substring(2)` | `"llo"` |
| `substring(int begin, int end)` | 截取指定范围（左闭右开） | `"Hello".substring(1, 4)` | `"ell"` |
| `split(String regex)` | 按分隔符拆分成数组 | `"a,b,c".split(",")` | `["a", "b", "c"]` |
| `join(CharSequence delimiter, CharSequence... elements)` | 用分隔符拼接（静态方法） | `String.join("-", "a", "b", "c")` | `"a-b-c"` |

---

## 三、转换类

| 方法 | 作用 | 示例 | 结果 |
|------|------|------|------|
| `toUpperCase()` | 转大写 | `"hello".toUpperCase()` | `"HELLO"` |
| `toLowerCase()` | 转小写 | `"HELLO".toLowerCase()` | `"hello"` |
| `trim()` | 去除首尾空白字符 | `"  hello  ".trim()` | `"hello"` |
| `strip()` | 去除首尾空白（Java 11+，支持 Unicode 空白） | `"  hello  ".strip()` | `"hello"` |
| `toCharArray()` | 转成字符数组 | `"abc".toCharArray()` | `['a', 'b', 'c']` |
| `getBytes()` | 转成字节数组 | `"abc".getBytes()` | `[97, 98, 99]` |
| `valueOf(任意类型)` | 把任意类型转成字符串（静态方法） | `String.valueOf(123)` | `"123"` |
| `replace(old, new)` | 替换所有匹配的子串 | `"hello".replace("l", "L")` | `"heLLo"` |
| `replaceAll(regex, replacement)` | 用正则表达式替换 | `"a1b2c".replaceAll("\\d", "#")` | `"a#b#c"` |

---

## 四、比较类

| 方法 | 作用 | 示例 | 结果 |
|------|------|------|------|
| `equals(Object obj)` | 判断内容是否相等（区分大小写） | `"Hello".equals("hello")` | `false` |
| `equalsIgnoreCase(String str)` | 判断内容是否相等（不区分大小写） | `"Hello".equalsIgnoreCase("hello")` | `true` |
| `compareTo(String str)` | 按字典序比较，返回差值 | `"abc".compareTo("abd")` | `-1` |
| `compareToIgnoreCase(String str)` | 按字典序比较（不区分大小写） | `"abc".compareToIgnoreCase("ABC")` | `0` |

> ⚠️ **重要**：String 比较**不能用 `==`**！`==` 比较的是内存地址，`equals()` 比较的才是内容。

---

## 五、拼接类

| 方法 | 作用 | 示例 | 结果 |
|------|------|------|------|
| `concat(String str)` | 拼接字符串 | `"Hello".concat(" World")` | `"Hello World"` |
| `+` 运算符 | 拼接（最常用） | `"Hello" + " World"` | `"Hello World"` |
| `StringBuilder` / `StringBuffer` | 大量拼接时用（性能更好） | 见下方说明 | — |

---

## 六、判断类

| 方法 | 作用 | 示例 | 结果 |
|------|------|------|------|
| `isBlank()` | 判断是否为空或全是空白（Java 11+） | `"   ".isBlank()` | `true` |
| `matches(String regex)` | 判断是否匹配正则表达式 | `"123".matches("\\d+")` | `true` |

---

## 七、若依项目中最常用的 String 方法

在若依项目的日常开发中，以下方法使用频率最高：

| 方法 | 使用场景 |
|------|---------|
| `equals()` | 判断字符串是否相等（如判断状态、类型） |
| `isEmpty()` / `isBlank()` | 判空校验（参数是否为空） |
| `substring()` | 截取字符串（如截取文件扩展名） |
| `split()` | 分割字符串（如分割逗号分隔的 ID） |
| `replace()` / `replaceAll()` | 替换内容（如路径替换、敏感词过滤） |
| `format()` | 格式化字符串（如日志输出） |
| `valueOf()` | 类型转换（数字转字符串） |
| `trim()` / `strip()` | 去除用户输入的首尾空格 |

---

## 一句话总结

> `String` 类的方法可以分为 **6 大类**：获取信息（`length`、`indexOf`）、截取分割（`substring`、`split`）、转换（`toUpperCase`、`replace`）、比较（`equals`）、拼接（`+`、`concat`）、判断（`isEmpty`、`matches`）。在若依项目中，`equals()`、`isEmpty()`、`split()`、`substring()` 是日常开发中最常用的四个方法。