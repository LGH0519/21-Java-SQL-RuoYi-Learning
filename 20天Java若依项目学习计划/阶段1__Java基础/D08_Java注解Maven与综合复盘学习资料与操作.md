# Day 08：Java 注解、Maven 与档案借阅控制台版

> 今日目标：知道注解和 `pom.xml` 分别在告诉框架/构建工具什么；用前 7 天知识完成一个小型借阅流程。只读若依源码，不改依赖。

## 知识点速记（完成当天任务后复习）

### 1. 注解不是普通业务代码

注解以 `@` 开头，是附着在类、方法、字段等位置的**元数据/标记**。框架或工具读取它后采取对应行为；注解本身不是“自动执行的业务流程”。

你今天在真实控制器中只需准确理解：

| 注解 | 位置 | 本机字典控制器中的作用 |
|---|---|---|
| `@RestController` | 类 | 这是处理 HTTP 请求的控制器，方法返回值通常写入响应。 |
| `@RequestMapping("/system/dict/type")` | 类 | 给该控制器的接口添加共同 URL 前缀。 |
| `@GetMapping("/list")` | 方法 | 表示该方法处理 GET 类型的 `/list` 路径。 |
| `@PreAuthorize("@ss.hasPermi('system:dict:list')")` | 方法 | 在执行方法前由安全框架检查对应权限。 |

今天不要求你会写 Spring 注解，更不要求理解 HTTP、权限表达式或 Spring 生命周期；先会从“注解位置 → 作用范围 → 大意”读代码。

### 2. Maven 与 `pom.xml`

Maven 是 Java 项目的构建和依赖管理工具；`pom.xml` 是它的核心配置文件，不是 Java 源码。

| 区块 | 作用 | 今天在若依根 `pom.xml` 中看到什么 |
|---|---|---|
| `<properties>` | 集中定义版本/属性。 | Java、Spring Boot、若依等版本属性。 |
| `<dependencyManagement>` | 统一管理子模块依赖的版本；通常不等于直接引入。 | 多个依赖版本约束。 |
| `<modules>` | 声明多模块工程由哪些模块组成。 | `ruoyi-admin`、`ruoyi-framework`、`ruoyi-system` 等。 |
| `<dependency>` | 实际声明某模块需要的库。 | 今天只认识，后续读模块 pom 再深入。 |

**易混点：** Maven 不等于 JDK；JDK 负责编译/运行 Java，Maven 按 `pom.xml` 下载依赖并组织构建。不要为“试试”升级项目中的任何版本。

### 3. 今天的小项目流程

```text
档案列表（List）
  → 按编号逐条查找（循环）
  → 找不到：返回失败文字
  → 找到但 canBorrow=false：返回失败文字
  → 可借：修改状态为 BORROWED，返回成功文字
```

这是控制台演练，不是若依真实接口；它的意义是把条件、循环、对象、方法、集合、异常前的基本功串起来。

## 资料与本机只读路径

| 资源 | 今天的操作 |
|---|---|
| [Java 零基础视频](https://www.bilibili.com/video/BV1Ho4y1f7Gd/) | 只看“注解”概念选集，跳过反射和框架注解实战。 |
| [Maven POM 中文说明](https://www.runoob.com/maven/maven-pom.html) | 只看 POM、`groupId/artifactId/version`、依赖的概念。 |
| [Maven 生命周期中文说明](https://www.runoob.com/maven/maven-build-life-cycle.html) | 只认得 `clean/compile/test/package` 名称，不执行。 |
| `backend/pom.xml` | `E:\WorkSpace\ruoyi-archive-management\backend\pom.xml`，只读 `properties/dependencyManagement/modules`。 |
| `SysDictTypeController.java` | `E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-admin\src\main\java\com\ruoyi\web\controller\system\SysDictTypeController.java`，只读指定四个注解。 |

## 今天照做（09:00–23:00）

### 09:00–12:00：注解与 Maven 观察任务

1. 看“注解”概念视频和两个 Maven 文档；不修改、不执行 `mvn clean`，不升级任何依赖。
2. 打开根 `pom.xml`，只完成下表笔记（不必抄具体版本号）：

| 位置 | 你看到的内容 | 你的解释 |
|---|---|---|
| `<properties>` | 至少写 2 个属性名 | 统一存放版本/配置值。 |
| `<dependencyManagement>` | 写“依赖版本管理” | 让子模块可复用统一版本。 |
| `<modules>` | 写出 3 个 module 名 | 根工程包含的子模块。 |

3. 打开 `SysDictTypeController.java`，找到 `@RestController`、类上的 `@RequestMapping`、`list` 方法上的 `@GetMapping` 与 `@PreAuthorize`。每个写一句“它标在谁身上、表达什么”。

**验收：** 能说清“注解给框架的标记；pom 给 Maven 的配置”，不把二者混为 Java 业务逻辑。

### 15:00–18:00：写档案借阅控制台版

新建 `ArchiveBorrowConsole.java`。它依赖 D05 的 `Archive`，需先确认 `Archive` 有 `getArchiveNo()`、`getName()`、`getStatus()`、`setStatus()`、`canBorrow()`。

```java
import java.util.ArrayList;
import java.util.List;

public class ArchiveBorrowConsole {
    public static Archive findByNo(List<Archive> archives, String archiveNo) {
        for (Archive archive : archives) {
            if (archive.getArchiveNo().equals(archiveNo)) {
                return archive;
            }
        }
        return null;
    }

    public static String applyBorrow(List<Archive> archives, String archiveNo) {
        Archive archive = findByNo(archives, archiveNo);
        if (archive == null) {
            return "借阅失败：未找到档案";
        }
        if (!archive.canBorrow()) {
            return "借阅失败：档案当前不可借";
        }
        archive.setStatus("BORROWED");
        return "借阅成功：" + archive.getName();
    }

    public static void main(String[] args) {
        List<Archive> archives = new ArrayList<>();
        archives.add(new Archive("DA-001", "采购合同", "AVAILABLE", 1, "admin", "2026-08-30"));
        archives.add(new Archive("DA-002", "验收报告", "BORROWED", 1, "admin", "2026-08-30"));

        System.out.println(applyBorrow(archives, "DA-001"));
        System.out.println(applyBorrow(archives, "DA-001"));
        System.out.println(applyBorrow(archives, "DA-999"));
    }
}
```

**预期输出：** 第一次成功；第二次因状态已改为 `BORROWED` 失败；第三次因找不到编号失败。

### 19:00–20:30：按流程 Debug

1. 在 `Archive archive = findByNo(...)` 打断点，Debug 启动第一次调用。
2. F7 进入 `findByNo`；看 `archives` 的两项、循环中的 `archiveNo` 与当前对象编号。
3. 回到 `applyBorrow` 后，观察 `archive` 是否为 `null`；F8 经过 `canBorrow` 分支。
4. 在 `archive.setStatus("BORROWED")` 后观察同一对象的 `status` 已改变。
5. 对第二次调用重复，说明失败是 `canBorrow=false`，不是“没找到”。

### 20:30–23:00：复盘与小改动

1. 增加一条 `AVAILABLE` 且 `remainingCopies=0` 的档案，调用借阅；解释为什么失败。
2. 在 `findByNo` 内故意写 `==` 比较编号，运行后改回 `equals`；解释该错误可能造成什么问题。
3. 完成三栏复盘：控制台代码的“数据在哪里”“判断在哪里”“状态在哪一行改变”。
4. 不看资料回答：`@GetMapping` 与 `@RequestMapping` 的层级关系；`dependencyManagement` 是否等于直接引用依赖；为什么此阶段不改 `pom.xml`。

AI 只能用于：贴出明确报错或断点变量后，请它解释“为什么进入这个分支”；禁止让它直接生成完整若依借阅模块。

## 今日完成清单

- [ ] 已完成 `pom.xml` 和控制器注解的只读笔记。
- [ ] 能分别解释四个注解和三个 pom 区块。
- [ ] `ArchiveBorrowConsole` 完成三条预期结果。
- [ ] Debug 中区分了“编号不存在”和“状态不可借”。
- [ ] 未改若依源码、`pom.xml`、版本或依赖。

---

## 任务参考答案（完成后再查看）

| 问题 | 参考答案 |
|---|---|
| `@RequestMapping` 与 `@GetMapping` | 前者在类上提供共同路径前缀；后者在方法上说明 GET 子路径，组合后形成完整接口路径。 |
| `dependencyManagement` | 主要统一管理版本，不等于当前模块已经直接可使用该依赖。 |
| 第一次借阅成功后第二次失败 | 两次查到的是同一个 `Archive` 对象；第一次把状态改成 `BORROWED`。 |
| 不改 pom 的原因 | 当前若依已运行；版本变更会引入与当天学习无关的环境问题。 |

**今日通过标准：** 能沿着“集合 → 查找 → 条件 → 改状态 → 返回文本”讲清楚控制台借阅流程。
