### 🔧 一、Node.js 具体在若依前端项目中做了什么？

|场景|Node.js 的作用|
|---|---|
|**💻 开发时**​|启动一个**本地开发服务器**（`npm run dev`），让你在浏览器中实时预览页面，并支持热更新|
|**📦 安装依赖**​|使用 **npm**（Node.js 自带的包管理器）下载项目所需的第三方 JS 库（如 `Vue` 本身、`Element Plus` 组件库等）|
|**🚀 打包上线**​|运行 **Vite**​ 或 **Webpack**​ 等构建工具，把源代码**编译、压缩、合并**成浏览器能直接运行的 HTML/CSS/JS 文件|

结合你之前学习的 **Spring Boot 后端启动原理**，可以把前后端的启动逻辑做一个对比，形成完整的全栈认知：

| 维度        | 🖥️ 后端 (Spring Boot / 若依)     | 🌐 前端 (Node.js / 若依-Vue)   |
| --------- | ----------------------------- | -------------------------- |
| **运行环境**​ | JDK (JVM 虚拟机)                 | Node.js (V8 引擎)            |
| **启动命令**​ | `java -jar ruoyi-admin.jar`   | `npm run dev`              |
| **内置服务**​ | 内置 Tomcat (Web 服务器)           | 内置 Vite/Webpack Dev Server |
| **打包产物**​ | `.jar` 或 `.war` 文件            | `dist` 文件夹 (HTML/CSS/JS)   |
| **自动配置**​ | Spring Boot AutoConfiguration | Vite/Webpack 插件与 Loader    |
|           |                               |                            |

> [!TIP] 若依前后端分离架构的运作流程
> 
> 1. **开发阶段**：后端跑 `RuoyiApplication.java` 提供接口；前端跑 `npm run dev` 提供页面。
> 2. **代理转发**：前端开发服务器通过 `vite.config.js` (或 `vue.config.js`) 中的 `proxy`，把 `/dev-api` 开头的请求转发给后端的 Tomcat 服务，解决跨域问题。
> 3. **上线阶段**：前端 `npm run build` 打包出 `dist` 文件夹，后端把 `dist` 里的静态资源托管，或者直接部署到 Nginx 上，由 Nginx 转发请求给后端的 jar 包。
