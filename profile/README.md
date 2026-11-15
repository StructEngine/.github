# StructEngine

**现实与体素的边界引擎 | 声明式 AI 自动化构建系统**

StructEngine 是一个致力于打破现实数据、AI 算力与 Minecraft 物理空间边界的开源底层引擎生态。基于“云端算力调度 + 边缘无状态执行”的解耦架构，将自然语言（Prompt）、2D/3D 空间数据（Google Street/Earth）直接编译、降维并投影为游戏内精确的三维方块结构。

我们坚信“言出法随，万物皆可体素化”的极客美学。

---

## 🏗️ 核心架构理念 (Core Philosophy)

StructEngine 摒弃了传统的“重度游戏插件”开发模式，采用了企业级微服务中的**端云分离**与**充血模型**设计：

* **平台掌控全局 (Platform as the Brain)**：所有的地图交互选址、AI 多模态大模型调用、3D 网格解析、并发网络 I/O 均由基于 JDK 21 的独立 Web 核心服务端完成。
* **游戏端边缘执行 (Plugin as the Hand)**：游戏服务器上的插件被剥离了所有高负载计算，降级为纯粹的“无状态渲染节点”。它仅通过 WebSocket 被动接收 `[id, x, y, z]` 绝对坐标指令，并执行原生的高效分批次渲染。
* **多平台依赖倒置 (DIP)**：核心构建算法使用纯 Java 编写，通过六边形架构的 SPI 接口无缝桥接 Fabric、NeoForge 和 Paper 等主流服务端 API。

---

## 📦 组织项目矩阵 (Repositories)

### 1. [StructEnginePlatform](https://github.com/StructEngine/StructEnginePlatform)

**核心算力中枢与 Web 交互管理平台**

整个生态的“大脑”。负责接收外部输入、统筹计算并将构建指令下发给各个 Minecraft 服务器节点。

* **前端引擎**：基于 Vue 3 + Element Plus，深度集成 Squaremap（Leaflet），实现强制像素化渲染（Pixelated）的上帝视角 2D 地图与精确的方块级选址。
* **后端调度**：基于 Spring Boot 3 + JDK 21 虚拟线程。零阻塞处理高并发 WebSocket RPC 调用，统筹游戏服 Y 轴高度探测与 AI 生成流转。
* **AI 算力**：集成 Spring AI，处理多模态提示词（Prompt）与 JSON 结构化空间模型生成。
* **街景解析 (`google-street` 模块)**：集成 JglTF 与 JOML，利用纯 Java 并发解析 Google Photorealistic 3D Tiles 模型，将其降维体素化并输出至游戏世界（开发中）。

### 2. [StructEngine](https://github.com/StructEngine/StructEngine)

**高性能、跨平台 Minecraft 自动化渲染引擎**

整个生态的“双手”。运行在 Minecraft 服务端上的多模块（Multi-module）插件工程，负责物理方块的最终落地。

* **Common 核心**：纯 Java 21 实现，包含 WebSocket Client 双向通信（响应高度查询与接收任务）、异步 JSON 模型下载、任务队列引擎以及回滚/恢复（Rollback/Resume）事务管理器。
* **Fabric / NeoForge / Paper 适配器**：极其轻薄的平台桥接层。通过注入 `IWorldAdapter` 和 `IServerScheduler`，将 Common 核心的安全异步方块放置操作完美适配到各大主线程 Server Tick 中。
* **零性能损耗**：采用限流队列与分批渲染算法，即使一次性接收 10 万个方块的构建任务，也能确保服务器 TPS 稳如泰山。

---

## 🛠️ 技术栈基准 (Tech Stack)

* **Language**: Java 21 LTS, TypeScript
* **Frameworks (Web)**: Spring Boot 3.2+, Vue 3.x, Spring AI
* **Frameworks (Game)**: Fabric API, Paper API, NeoForge
* **Build Tools**: Maven (Platform), Gradle (Plugin)
* **3D / Map**: Leaflet.js, JglTF, JOML

## 💡 工作流演示 (Workflow)

1. **Targeting (选址)**: Web 侧管理员在 Squaremap 地图上点击目标方块，录入提示词（如“生成一座中式塔楼”）。
2. **Probing (探测)**: Platform 挂起虚拟线程，通过 WebSocket 向游戏端发起 `QUERY_Y` RPC 调用，游戏端在主线程拿到地表精确高度后秒回。
3. **Compiling (编译)**: Platform 将完整的 `(X, Y, Z)` 绝对坐标送入模型生成管线，落盘生成 `.json` 体素数据流。
4. **Executing (执行)**: Platform 下发 `BUILD` 指令，游戏端插件下载数据、压入队列，在玩家眼前平滑地将建筑渲染成型。

---

*“Code the rules, generate the world.”*
