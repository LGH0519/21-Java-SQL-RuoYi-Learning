# `SpringApplication.run()` 源码逐行深度解析

---

## 整体结构：先看清"骨架"

这个 `run()` 方法可以分成 **4 大阶段**：

```
准备阶段 → 核心启动阶段（try 块） → 异常处理 → 收尾阶段
```

---

## 第一阶段：准备阶段（启动前的准备工作）

### ① 启动计时器

```java
Startup startup = Startup.create();
```

创建一个计时器，用来记录"从启动到完成花了多少时间"。

> 类比：就像比赛开始前按下秒表——"预备，开始！"

---

### ② 注册关闭钩子

```java
if (this.properties.isRegisterShutdownHook()) {
    SpringApplication.shutdownHook.enableShutdownHookAddition();
}
```

**关闭钩子（Shutdown Hook）** = 告诉 JVM："如果用户按了 Ctrl+C 或关闭程序，请先执行清理工作再退出。"

| 情况 | 行为 |
|------|------|
| `true`（默认） | 程序关闭时会自动关闭数据库连接、释放资源等 |
| `false` | 程序直接退出，可能留下未关闭的资源 |

> 类比：就像下班前的"关窗锁门"检查——确保走之前把所有东西收拾好。

---

### ③ 创建引导上下文

```java
DefaultBootstrapContext bootstrapContext = createBootstrapContext();
```

创建一个**临时的"引导环境"**，用于在正式容器启动之前做一些前期准备工作（比如加载某些早期需要的配置）。

> 类比：就像开业前的"临时指挥部"——正式办公室还没布置好，先在帐篷里指挥前期工作。

---

### ④ 声明容器变量

```java
ConfigurableApplicationContext context = null;
```

声明一个**应用上下文**（ApplicationContext）变量，初始值为 `null`。这个变量后面会被赋值，它就是 Spring 的"大容器"——所有 Bean 都住在这里面。

> 类比：就像提前准备好一个"空仓库"的标签，等下要把所有货物（Bean）搬进去。

---

### ⑤ 配置无头模式

```java
configureHeadlessProperty();
```

设置 Java 的 **Headless 模式**（无头模式）。意思是：这个程序运行在服务器上，没有显示器、没有键盘、没有鼠标。

> 类比：就像告诉系统"这是一台服务器，不要尝试弹出任何窗口"。

---

### ⑥ 获取监听器并通知"开始启动"

```java
SpringApplicationRunListeners listeners = getRunListeners(args);
listeners.starting(bootstrapContext, this.mainApplicationClass);
```

| 代码 | 做了什么 | 类比 |
|------|---------|------|
| `getRunListeners(args)` | 找到所有"监听器"（Listener） | 召集所有"观察员"到场 |
| `listeners.starting(...)` | 通知所有监听器："我要开始启动了！" | 广播："比赛马上开始！" |

**监听器（Listener）** 是一种"观察者"机制——某些组件想"监听"启动过程中的各个阶段，在特定时刻做自己的事情（比如打印日志、记录指标等）。

---

## 第二阶段：核心启动阶段（try 块内）

### ⑦ 封装命令行参数

```java
ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
```

把命令行传入的 `args` 封装成一个 `ApplicationArguments` 对象，方便后续统一处理。

> 类比：把散落的纸条（命令行参数）整理成一份正式的"需求单"。

---

### ⑧ ⭐ 准备环境 —— 读取配置文件

```java
ConfigurableEnvironment environment = prepareEnvironment(listeners, bootstrapContext, applicationArguments);
```

**这是"读取配置文件"发生的地方！** 内部做了这些事：

```
prepareEnvironment()
    │
    ├── 创建 Environment 对象（环境对象）
    │
    ├── 加载 application.yml          ← 你的主配置文件
    │       ├── server.port: 8080
    │       ├── spring.profiles.active: druid
    │       └── ...
    │
    ├── 加载 application-druid.yml     ← 被激活的 druid 配置
    │       ├── spring.datasource.druid.master.url
    │       ├── spring.datasource.druid.master.username
    │       └── ...
    │
    └── 合并所有配置源（yml + 命令行参数 + 系统属性）
```

> 类比：就像把"工作手册"（application.yml）、"附录"（application-druid.yml）、"老板口头指示"（命令行参数）全部合并成一份"最终执行方案"。

---

### ⑨ 打印 Banner

```java
Banner printedBanner = printBanner(environment);
```

打印启动时的 Banner（就是 `application.yml` 中配置的或默认的 Spring Boot 图案）。

> 类比：就像酒店开业时门口挂的"开业大吉"横幅。

---

### ⑩ ⭐ 创建应用上下文 —— 搭建容器

```java
context = createApplicationContext();
context.setApplicationStartup(this.applicationStartup);
```

**创建 Spring 容器（ApplicationContext）**。根据应用类型（Web/非Web）创建不同的容器：

| 应用类型 | 创建的容器 | 说明 |
|---------|-----------|------|
| SERVLET（Web 应用） | `AnnotationConfigServletWebServerApplicationContext` | 支持 Web 服务器 |
| REACTIVE | `AnnotationConfigReactiveWebServerApplicationContext` | 响应式 Web |
| 普通应用 | `AnnotationConfigApplicationContext` | 无 Web 功能 |

若依是 Web 应用，所以创建的是第一种。

> 类比：就像根据"酒店"还是"仓库"来建造不同的大楼——酒店要有前台（Web 服务器），仓库不需要。

---

### ⑪ ⭐ 准备上下文 —— 加载注解、排除配置、注册 Bean

```java
prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
```

**这是"处理 @SpringBootApplication 注解 + 排除 DataSourceAutoConfiguration"发生的地方！** 内部做了：

```
prepareContext()
    │
    ├── 把 Environment 设置到容器中
    ├── 调用 ApplicationContextInitializer（初始化器）
    ├── 通知监听器：context 已准备
    │
    ├── ⭐ 加载 BeanDefinition（Bean 定义）
    │       ├── 读取 @SpringBootApplication 注解
    │       ├── 发现 exclude = { DataSourceAutoConfiguration.class }
    │       ├── 从自动配置列表中移除它
    │       └── 注册所有组件的 BeanDefinition
    │
    └── 设置 Banner 到容器中
```

> 类比：就像"智能管家"翻开自动安装清单，发现"数据源"那项被划掉了，就跳过它。

---

### ⑫ ⭐⭐ 刷新上下文 —— 最核心的一步

```java
refreshContext(context);
```

**这是整条流水线中最重要的方法！** "读取配置文件 → 创建数据源 → 加载所有组件 → 启动 Web 服务器"这一整条流水线，大部分都在这一步完成。

`refreshContext()` 内部调用 `AbstractApplicationContext.refresh()`，按顺序执行：

```
refresh()
    │
    ├── ① prepareRefresh()
    │       └── 记录启动时间、设置标志位
    │
    ├── ② obtainFreshBeanFactory()
    │       └── 获取 Bean 工厂（Bean 的"生产车间"）
    │
    ├── ③ prepareBeanFactory(beanFactory)
    │       └── 配置 Bean 工厂的标准特性
    │
    ├── ④ postProcessBeanFactory(beanFactory)
    │       └── 子类可以在此处添加自定义处理
    │
    ├── ⑤ invokeBeanFactoryPostProcessors(beanFactory)  ⭐
    │       ├── 加载 DruidConfig.java
    │       ├── 创建 masterDataSource（主数据源）
    │       ├── 创建 slaveDataSource（从数据源，如果启用）
    │       └── 创建 DynamicDataSource（动态数据源）
    │
    ├── ⑥ registerBeanPostProcessors(beanFactory)
    │       └── 注册 Bean 后处理器（AOP 等）
    │
    ├── ⑦ initMessageSource()
    │       └── 初始化国际化消息源
    │
    ├── ⑧ initApplicationEventMulticaster()
    │       └── 初始化事件广播器
    │
    ├── ⑨ onRefresh()  ⭐⭐
    │       └── 启动内嵌 Tomcat Web 服务器（8080 端口）
    │
    ├── ⑩ registerListeners()
    │       └── 注册应用监听器
    │
    ├── ⑪ finishBeanFactoryInitialization(beanFactory)  ⭐⭐
    │       ├── 实例化所有非懒加载的单例 Bean
    │       ├── 扫描 @Controller、@Service、@Repository 等
    │       ├── 创建所有 Bean 的实例
    │       └── 完成依赖注入（DI）
    │
    ── ⑫ finishRefresh()
            └── 发布 ContextRefreshedEvent 事件
```

其中**三个关键子步骤**对应你问的流水线：

| 流水线环节 | 对应 refresh() 的子步骤 | 做了什么 |
|-----------|----------------------|---------|
| **创建数据源** | ⑤ `invokeBeanFactoryPostProcessors()` | 加载 `DruidConfig`，创建主/从/动态数据源 |
| **启动 Web 服务器** | ⑨ `onRefresh()` | 创建并启动内嵌 Tomcat，监听 8080 端口 |
| **加载所有组件** | ⑪ `finishBeanFactoryInitialization()` | 实例化所有 Bean，完成依赖注入 |

> 类比：`refresh()` 就像酒店的"全面开业准备"——⑤ 安装水管系统（数据源）、⑨ 打开大门（Web 服务器）、⑪ 所有员工到岗就位（Bean 实例化）。

---

### ⑬ 启动后处理

```java
afterRefresh(context, applicationArguments);
```

刷新完成后的钩子方法，默认是空的，留给子类扩展。

---

### ⑭ 记录启动时间

```java
Duration timeTakenToStarted = startup.started();
if (this.properties.isLogStartupInfo()) {
    new StartupInfoLogger(this.mainApplicationClass, environment).logStarted(getApplicationLog(), startup);
}
```

计算从启动到现在花了多少时间，打印日志（比如 "Started RuoYiApplication in 5.123 seconds"）。

---

### ⑮ 通知监听器"已启动" + 执行 Runner

```java
listeners.started(context, timeTakenToStarted);
callRunners(context, applicationArguments);
```

| 代码 | 做了什么 |
|------|---------|
| `listeners.started(...)` | 通知监听器："容器已启动完成！" |
| `callRunners(...)` | 执行所有实现了 `CommandLineRunner` 或 `ApplicationRunner` 接口的类 |

> 类比：⑮ 就像"开业剪彩"——所有准备工作完成，正式宣布开业，然后执行"开业仪式"（Runner）。

---

## 第三阶段：异常处理

```java
catch (Throwable ex) {
    throw handleRunFailure(context, ex, listeners);
}
```

如果启动过程中任何一步出错，统一处理异常并抛出。

---

## 第四阶段：收尾

```java
try {
    if (context.isRunning()) {
        listeners.ready(context, startup.ready());
    }
}
catch (Throwable ex) {
    throw handleRunFailure(context, ex, null);
}
return context;
```

- 确认容器正在运行
- 通知监听器："应用已就绪，可以接客了！"
- 返回 `context`（Spring 容器）

---

## 完整流水线映射图

```
run() 方法
  │
  ├── ① 启动计时器
  ├── ② 注册关闭钩子
  ├── ③ 创建引导上下文
  ├── ④ 声明容器变量
  ├── ⑤ 配置无头模式
  ├── ⑥ 获取监听器 + 通知"开始启动"
  │
  ├── ⑦ 封装命令行参数
  ├── ⑧ prepareEnvironment()        ──→ 📖 读取配置文件（application.yml + druid）
  ├── ⑨ printBanner()               ──→  打印 Banner
  ├── ⑩ createApplicationContext()  ──→ 🏗️ 创建 Spring 容器
  ├── ⑪ prepareContext()            ──→ ⚙️ 处理注解、排除数据源自动配置
  ├── ⑫ refreshContext()            ──→ 🔥 核心！
  │       ├── invokeBeanFactoryPostProcessors()  ──→ 🔧 创建数据源（DruidConfig）
  │       ├── onRefresh()                        ──→ 🌐 启动 Tomcat（8080 端口）
  │       └── finishBeanFactoryInitialization()  ──→ 📦 加载所有组件（Bean 实例化）
  ├── ⑬ afterRefresh()
  ├── ⑭ 记录启动时间
  ├── ⑮ 通知"已启动" + 执行 Runner
  │
  ├── ⑯ 通知"已就绪"
  └── ⑰ 返回 context
```

---

## 一句话总结

> `run()` 方法就像一条**自动化装配线**——从"按下启动按钮"到"开始对外服务"，中间经历了 17 个步骤。其中 `prepareEnvironment()` 负责读配置，`refreshContext()` 内部的三个子步骤分别负责创建数据源、启动 Web 服务器、加载所有组件。你写的代码只有 1 行（`SpringApplication.run()`），但背后是 Spring Boot 帮你自动完成了这一整条流水线。