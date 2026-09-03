# Day 06：Java List、Set、Map 与泛型

> 今日目标：知道多条档案该选哪种集合，能用编号快速查一条档案。基于 Day05 的 `Archive`，不学习数据库。

## 知识点速记（完成当天任务后复习）

| 结构         | 核心理解                       | 档案系统例子            | 常用操作                       |
| ---------- | -------------------------- | ----------------- | -------------------------- |
| `List<E>`  | 有顺序、可重复、按下标访问。             | 档案列表、审批列表。        | `add`、`get`、`size`、遍历。     |
| `Set<E>`   | 不保证重复元素；`HashSet` 不保证显示顺序。 | 用档案编号检测重复。        | `add` 返回是否添加成功。            |
| `Map<K,V>` | 键映射到值；键不能重复。               | `档案编号 → Archive`。 | `put`、`get`、`containsKey`。 |

**泛型**是尖括号里的元素类型，例如 `List<Archive>`：它告诉编译器列表只能放 `Archive`，取出时不必强制转换。`List` 是接口，`ArrayList` 是常用实现；声明时优先写左边接口类型：

```java
List<Archive> archives = new ArrayList<>();
Set<String> archiveNos = new HashSet<>();
Map<String, Archive> archiveMap = new HashMap<>();
```

| 易混点                  | 正确结论                             |
| -------------------- | -------------------------------- |
| `List` 会自动去重吗？       | 不会；相同对象/相同内容都可被加入。               |
| `HashSet` 是否按加入顺序输出？ | 不保证；今天只用它做去重。                    |
| `Map` 的 `get` 找不到时？  | 返回 `null`，使用前需判断。                |
| 泛型是运行时校验吗？           | 首先是编译期类型约束；今天理解“放错类型会在编译时被拦住”即可。 |

## 视频、文档与真实代码观察

| 资源                                                             | 只学习这些内容                                                                                                                                                            |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [Java 零基础视频](https://www.bilibili.com/video/BV1Ho4y1f7Gd/)     | “集合框架、ArrayList、HashSet、HashMap、泛型”。                                                                                                                               |
| [Java 集合框架](https://www.runoob.com/java/java-collections.html) | List/Set/Map 的角色。                                                                                                                                                  |
| [Java 教程目录](https://m.runoob.com/java/)                        | 从目录进入 ArrayList、HashSet、HashMap、泛型，只读基本增删查遍历。                                                                                                                      |
| 本机 `SysDictTypeController.java`                                | `E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-admin\src\main\java\com\ruoyi\web\controller\system\SysDictTypeController.java`；只观察 `List<SysDictType> list`。 |

## 今天照做（09:00–23:00）

### 09:00–12:00：先做选择题，再看视频

1. 看指定视频与文档，跳过 `LinkedList`、`TreeMap`、迭代器源码、并发集合。
2. 在笔记回答：
   - 要按页面顺序显示所有档案，用什么？`List`。
   - 要判断编号是否重复，用什么？`Set`。
   - 已知编号要立即找到档案，用什么？`Map`。
3. 打开真实控制器，找到 `List<SysDictType> list`。只写一句：`list` 里面每一项是一个 `SysDictType` 对象，`<SysDictType>` 限制了元素类型。

### 15:00–18:00：完成 `CollectionDemo.java`

> 若 Day05 的 `Archive` 构造方法仍是六个参数，按下方调用；不要新建同名 `Archive`。

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class CollectionDemo {
    public static void main(String[] args) {
        Archive first = new Archive("DA-001", "采购合同", "AVAILABLE", 1, "admin", "2026-08-30");
        Archive second = new Archive("DA-002", "验收报告", "BORROWED", 1, "admin", "2026-08-30");
        Archive third = new Archive("DA-003", "行政通知", "AVAILABLE", 2, "admin", "2026-08-30");

        List<Archive> archives = new ArrayList<>();
        archives.add(first);
        archives.add(second);
        archives.add(third);
        System.out.println("列表数量：" + archives.size());
        for (Archive archive : archives) {
            System.out.println(archive.getArchiveNo() + "：" + archive.getName());
        }

        Set<String> archiveNos = new HashSet<>();
        System.out.println("首次加入 DA-001：" + archiveNos.add("DA-001"));
        System.out.println("再次加入 DA-001：" + archiveNos.add("DA-001"));

        Map<String, Archive> archiveMap = new HashMap<>();
        archiveMap.put(first.getArchiveNo(), first);
        archiveMap.put(second.getArchiveNo(), second);
        archiveMap.put(third.getArchiveNo(), third);
        Archive found = archiveMap.get("DA-002");
        System.out.println("查询结果：" + found.getName());
    }
}
```

**验收：** 列表数量 3；Set 两次结果为 `true`、`false`；编号 `DA-002` 查询到“验收报告”。

### 19:00–20:20：Debug 数据结构

1. 在 `archives.add(third)` 后一行打断点；Debug 启动，展开 `archives`，确认有 3 个对象。
2. 在第二次 `archiveNos.add` 后一行打断点，确认 Set 大小仍是 1；不要根据输出顺序判断 Set 对错。
3. 在 `archiveMap.get("DA-002")` 后一行打断点，展开 `archiveMap`，确认键和值的对应关系。

### 20:20–23:00：三个小题与复盘

1. 将 `archives` 中的 `AVAILABLE` 数量用 `for-each` 统计出来；预期为 2。
2. 用 `archiveMap.containsKey("DA-999")` 判断不存在编号；不要直接对 `get` 结果调用方法。
3. 在 `List<Archive>` 上故意写 `archives.add("文字")`，阅读编译错误后删除该行；解释泛型帮你避免了什么。
4. 不看资料回答：List/Set/Map 各用于什么；Set 为何不能用于稳定排序展示；`get` 为什么要判空。

AI 提问模板：说明你使用的集合类型、加入的数据、期望的 `size/get` 结果、断点看到的实际内容；不要让 AI 替你更换成数据库。

## 今日完成清单

- [ ] 能按“列表/去重/编号查询”选择 List、Set、Map。
- [ ] `CollectionDemo` 运行正确。
- [ ] 已在 Debug 中展开 List、Set、Map。
- [ ] 已只读查看 `List<SysDictType>`，未改若依代码。
- [ ] 完成三个小题并能解释泛型。

---

## 任务参考答案（完成后再查看）

```java
int availableCount = 0;
for (Archive archive : archives) {
    if ("AVAILABLE".equals(archive.getStatus())) {
        availableCount++;
    }
}
System.out.println("可借档案数量：" + availableCount);

if (archiveMap.containsKey("DA-999")) {
    System.out.println(archiveMap.get("DA-999").getName());
} else {
    System.out.println("未找到档案");
}
```

**今日通过标准：** 能解释 `List<Archive>` 的 `<Archive>`，并能从 `Map<String, Archive>` 取回正确对象。
