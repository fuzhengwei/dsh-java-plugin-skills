---
name: dsh-java-plugin-skills
description: 辅助用户快速完成 deepseek-harness-java（DSH Java，Java Agent 运行时基座）与业务应用的智能体对接。可以按用户诉求从零开发任意领域的 Java AI 应用（智能客服、MySQL 运维平台、研学旅游、预订平台等）并以 Java Native 插件方式接入 DSH；也可以把用户已有 Java 应用插件化对接 DSH。支持启动/部署 Harness 服务与应用服务、安装激活插件、提供架构图/说明文档/面试资料。触发词：「对接 deepseek-harness-java」「DSH 插件开发」「Java Native Plugin」「智能客服接入」「开发一个 AI 应用」「XX 的 AI 应用/智能体」「插件化对接 Agent」「启动 DSH」「harness 插件安装」。
license: Apache-2.0
metadata:
  author: xfg-studio
  version: "1.11.0"
  category: agent-plugin
  homepage: https://github.com/fuzhengwei/deepseek-harness
---

# DSH Java 插件对接技能

本技能帮助用户**快速完成 deepseek-harness-java（下称 DSH）与业务应用的 AI 智能体对接**。两种典型场景：

1. **用户已有应用** → 为应用开发一个 Java Native 插件，注册工具到 DSH Agent，让 AI 能调用应用能力。
2. **用户没有应用** → 按诉求开发一个 Java 应用（如智能客服商城、MySQL 运维平台），再以插件方式接入 DSH。

最终交付：**探活确认可访问的** DSH 地址 + 应用地址 + 体验流程说明 + 完善的工程 README（含简历/面试沉淀），插件已安装激活且每个工具全链路实测通过。

## 核心概念（必读）

DSH 是 Java Agent 运行时基座（端口 8090），提供 Web 控制台、Agent 对话、模型配置、插件管理。插件是 **Java Native Plugin**：

- 插件 JAR 依赖 `cn.xiaofuge:deepseek-harness-java-types:0.1.5`（scope=provided，宿主提供）
- 继承 `AbstractHarnessPlugin`，在 `tools()` 中返回 `ToolDefinition` 列表（工具继承 `AbstractTool`，实现 `name/description/parameters/run`）
- 在 `configure(PluginContext)` 中注册系统提示词与 Hook（如 `PRE_TOOL_USE` 审计）
- 必须提供 `META-INF/plugin.yaml`（id/name/version/entrypoint）和 SPI 声明文件 `META-INF/services/cn.xiaofuge.deepseek.harness.domain.spi.JavaHarnessPlugin`
- 工具在 Agent 中以 `plugin__<pluginId>__<toolName>` 命名暴露
- 插件通过 HTTP 调用业务应用 API（不直接持有业务资源据，注意超时/异常分类/脱敏）

**本技能目录下已有现成材料，优先使用，不要凭记忆编造：**
- `runtime/deepseek-harness-java-app.jar` — DSH 宿主可执行 JAR，直接 `java -jar` 启动
- `references/plugin-dev-guide.md` — 插件开发全流程（含完整代码骨架，从两个真实案例提炼）
- `references/deploy-guide.md` — 启动部署指南（本地/服务器/无 Java 环境）
- `scripts/deploy_remote.sh` — 云服务器一键部署（上传 JAR → 生成远端 restart_all.sh → 启动 → 探活 → 输出重启/日志/插件激活指引），用户要云部署时必用
- `references/case-2d-weekend-mall.md` / `references/case-dsh-java-mysql.md` — 两个完整案例
- `references/ui-design-guide.md` — 应用 UI 设计指南（design tokens、分域设计语言表+设计语言卡、无 AI 味清单、AI 助手面板规范），生成前端必读
- `references/prompt-recipes.md` — 案例储备库（商城/金融/出行/外卖/点评/医疗/教育/政务/娱乐/资讯/智能硬件/创作/农业/环保/科技 + 工程效能（Redis/ES/发布监控/研发协作/测试用例/需求管理/值班巡检）+ 角色行业（教师/语数外学习/销售/医生/律师/招聘）+ 行业纵深与生活服务（快递物流/酒旅/财务报销/保险理赔/制造 MES/物业/宠物医院/汽车养护/供应链/房产中介）+ 扩展场景（二手交易/积分商城/直播复盘/餐厅经营/景区导览/婚庆/民宿/票务/充电站/车队/光伏/航班/投资看板/资产配置/众筹/体检/慢病/心理咨询/教务/培训/留学/知识库/OKR/IT 资产/合同/舆情/招投标/客服质检/热线/网格员/志愿者/ESG/实验室/工地/冷链/水质/导游/房东/营养师/自由职业/电竞/琴行/宠物寄养/装修/团购团长/图书馆/约拍）+ 视觉智能（拍照卡路里/风水点评/识花绿植/小票记账/车损估价）等 104 个一句话完整案例），用户询问"能做什么/举个例子"时抽 3~5 个展示；**视觉图像输入类场景必读其中「视觉场景实现规范」节**（image_id 传参/识别两路径/免责声明）
- `references/architecture.md` — 架构图与说明（可导出给用户）
- `references/interview-notes.md` — 面试资料
- `scripts/check_env.sh` — 环境检查（Java/Maven 版本，缺失时提示安装方式）
- `scripts/start_harness.sh` — 启动 DSH（自动检查端口 8090）
- `scripts/install_plugin.sh` — 安装+激活插件（curl 调 install/activate 接口）
- `scripts/smoke_test.sh` — 冒烟验证（检查服务、插件列表）
- `scripts/agent_stream.sh` — Agent 端到端流式调用（自动绕过本机代理，输出工具调用轨迹 + 最终回答，交付前验证必用）
- `scripts/delivery_check.sh` — 最终交付自动化检查（探活/插件状态/鉴权回归，阶段 4 收尾必跑）
- `references/delivery-checklist.md` — 最终交付清单（自动化项 + 手工项：UI 真实浏览器验证、回归矩阵、DSH 已知问题、交付物完整性，**收尾必过**）
- `references/runtime-pitfalls.md` — 运行环境坑位与端到端验证指南（**在沙箱/受限代理环境执行本技能前必读**）：SERVER_PORT 劫持、HTTP_PROXY 502、进程回收、DSH 运行时已验证事实（agentId 按需创建/默认模型渠道/SSE 事件名）、标准验证配方
- `references/readme-delivery-template.md` — README 交付模板 + 简历项目模板 + 技术关键词 + 面试重点（**阶段 5 必用**，含体验流程说明模板与 README 质量红线）

## 工作流程

### 阶段 0：澄清需求

**help 触发（优先级最高，先于第一问）**：用户说「help / 帮助 / 你能做什么 / 有什么能力 / 不知道做什么」时，不要直接抛二选一问题，按 `references/prompt-recipes.md`「帮助菜单」节回复：一句话能力介绍（两条路径 + 三种交付形态 + 一句话即可完成开发部署）→ 104 案例分类菜单全貌（含工程效能、角色行业、行业纵深、扩展场景与视觉智能板块）→ 引导话术（回复编号/名称开工，或直接描述任意其他应用）。首次接触必须让用户看到「还能选什么」，而不是猜一个再说。

**第一问（必问，二选一）**：你是想 **① 给现有的 Java 项目扩展 AI 能力**，还是 **② 做一个全新项目**？用选择框降低输入成本。

**第二问（必问，三选一，用选择框）：项目以什么形态交付？** 选项措辞必须用下面这套（实测「独立可运行项目」这类说法会被误解为更高配，禁用）：
1. **应用 + DSH 插件（AI 可调用）**——标准玩法：生成 `xxx-app`（业务应用）+ `xxx-plugin`（插件模块），插件把应用 API 注册为 Agent 工具，AI 在 DSH 对话中能查数据、做操作。三层结构：DSH Agent → 插件（对接器）→ 业务应用（HTTP）。说明中必须点明「应用和插件是一套交付的，插件就是 DSH 与应用之间的对接器」。
2. **仅独立应用（不接 AI）**——只生成 `xxx-app`，无插件、不进 DSH 插件列表，AI 调不到它。适合只要业务系统本身、不要智能体能力的场景。展示时明确说「功能照常能用，但与 DSH 无关」。
3. **先要设计 + 原型**——先不写代码，出实体表、工具清单、页面原型，用户确认方向后再开发。

**路径 ①：给现有 Java 项目扩展 AI**
1. 要项目本地路径或仓库地址，让用户一句话说明项目是什么
2. **亲自分析项目**（不要只听用户说）：读 README、pom.xml（技术栈/依赖/模块）、Controller/Service/领域模型，总结出：项目是做什么的、核心实体、已有 API 能力
3. **主动提供该项目的 AI 扩展场景清单**（3~6 个，这是本路径的核心价值）：每个场景包含——场景名、AI 能帮用户做什么（用业务语言）、建议拆成的工具（`tool_name` + 一句话入参出参）、为什么有价值（省什么时间/补什么短板）；用选择框让用户点选或组合，也可让用户自己补充
4. 用户选定后进入阶段 3（只需生成 `xxx-plugin` 模块，工具通过 HTTP 调既有应用 API；应用侧无 AI 面板时可建议加一个轻量 AI 入口，用户不要就不加）

**路径 ②：做新项目**
- 用户没想好做什么 → 读 `references/prompt-recipes.md`，**抽 3~5 个跨大分类案例**（完整话术原文展示，不要只给标题，每个附一句"AI 亮点"说明做出来的效果），说明"选定任意一个即可一句话完成开发+部署启动运行"；展示时优先用选择框（选项=案例名）让用户点选，降低输入成本；末尾留口"也可以直接描述任何其他应用"；同一会话重复询问时轮换案例，不要每次给同一批；用户选定或原样粘贴某条 prompt 后直接进入开发，不再追问
- 用户已有想法 → 明确业务领域与核心场景（如研学旅游：行程/路线/报名/订单；客服：商品/订单/物流），列出该领域 AI 应能查询和操作的核心实体清单

**两条路径共同要问的（不确定时才问，一次问完）**：
- 运行环境：本地 macOS/Linux 还是远程服务器？有没有 JDK 17+ / Maven？
- DSH 是否已在运行（`curl --noproxy '*' http://127.0.0.1:8090` 探活）？

**可默认、不必问**（直接采用并在交付时说明）：端口用 18081 起顺延（避开 18080/8091）；数据用内存 Map 预置演示数据（与 2d-weekend-mall 一致，零依赖快速跑通）；前端用原生 HTML/CSS/JS 单页；包名 `cn.xiaofuge.<domain>`。

### 阶段 1：环境准备
先读 `references/runtime-pitfalls.md`（若在沙箱/受限代理环境，其中 SERVER_PORT 劫持与 HTTP_PROXY 502 两个坑必然遇到）。然后：
```bash
bash <skill_path>/scripts/check_env.sh
```
缺 JDK 17 → 指导 `brew install openjdk@17`（macOS）或 `sudo apt-get install -y openjdk-17-jdk`（Ubuntu）；缺 Maven → `brew install maven` / `sudo apt-get install -y maven`。无本地环境也可用服务器部署（见 deploy-guide）。

### 阶段 2
```bash
bash <skill_path>/scripts/start_harness.sh
```
启动后打开 `http://127.0.0.1:8090`，「设置 → 模型设置 → 添加模型」配置模型地址/名称/API Key，否则 Agent 无法对话。

### 阶段 3：开发应用与插件
**写码前必须先做场景深挖**（`references/prompt-recipes.md` 的「场景深挖」节）：实体表 → 工具表（入参/出参/description）→ 页面区块 → 预置数据 → 参照标杆（电商类参照 mall，UI 参照 50projects50days 与本技能案例源码），形成设计稿后再动手。
**设计稿确认点（必做）**：设计稿完成后先给用户过目（实体/工具/页面/数据四块，紧凑呈现），用选择框确认「开工 / 要调整」，用户明确说「直接做」才可跳过——禁止闷头写码后才发现方向跑偏。
**预置数据必须按「细腻度规范」写**（`references/prompt-recipes.md` 末节）：名称有品牌/编号、价格真实区间、数据间有故事、状态机完整；交付前逐条过 7 项验收清单，出现"路线A/商品A/示例数据"即为不合格重做。
- 无应用：按 `references/plugin-dev-guide.md` 的 Spring Boot 应用骨架 + 插件骨架生成工程（Maven 多模块：`xxx-app` + `xxx-plugin`）
- 有应用：只生成 `xxx-plugin` 模块，工具通过 HTTP 调应用 API
- 遵循两个案例的模式（见 case 文档）：plugin.yaml + SPI + AbstractHarnessPlugin + AbstractTool，提供构建命令 `mvn package -DskipTests`，构建必须亲自执行并确认 JAR 生成

**全新业务应用（任意领域，如研学旅游/预订平台/内容社区）设计范式**（从案例泛化，必须遵守）：
1. **先列实体再设计工具**：从业务场景提取核心实体（如研学旅游 = 营地/路线/排期/报名订单），每个"查询/详情/状态"类实体操作对应一个工具，通常 3~6 个
2. **工具命名动词+宾语**：如 `search_routes`、`route_detail`、`enrollment_query`、`itinerary_query`；只读查询优先，写操作（报名/下单）需在 description 中声明风险
3. **app 模块最小闭环**：REST API（list/detail/create）+ 内存预置数据（8 条左右）+ 单页前端（业务主界面 + AI 助手面板代理 DSH `/api/agent/stream`，参考 case-2d-weekend-mall 模式）
   - **UI 必须遵守 `references/ui-design-guide.md`**：先定 design tokens（领域主色+暖白底+大圆角+系统字体栈），界面要像认真做过的产品，无 AI 味（禁止默认蓝紫渐变/emoji 图标/裸表格/Lorem ipsum）；**必须先声明「设计语言卡」**（主色/背景材质/形状母题/标志性元素/气质关键词，按 ui-design-guide.md 1.5 分域设计语言表选或自创），同类案例禁止撞脸（辅助色/材质/布局至少两项不同），AI 面板配色跟随本应用设计语言；AI 面板用右下角浮动按钮+侧滑流式渲染，**AI 气泡必须走 markdown 渲染**（`renderMd` 轻量渲染器直接抄 ui-design-guide.md 第五节，禁止 `textContent` 裸显模型输出的 `**加粗**`/列表/标题；流式期间逐 chunk 实时渲染）
4. **系统提示词划边界**：写明该业务工具何时必须调用（涉及真实数据必须查证）、禁止编造
5. **application.yml 预留**：`harness-base-url`、`agent-id`、`service-token`（与插件配置对应）；改 token 后需停启插件

### 阶段 4：安装插件、全链路验证并交付
```bash
bash <skill_path>/scripts/install_plugin.sh <plugin_jar路径> <pluginId> <版本> <入口Jar文件名>
bash <skill_path>/scripts/smoke_test.sh
```
**全链路功能验证（功能开发完成后必做，不是只验一条 happy path）**：
- **每个工具至少一条自然语言触发实测**：用 `agent_stream.sh` 逐个工具验证（读工具各 1 条不同问法；写工具 1 条 + 确认机制验证），确认 step_break 事件里 toolName 正确、result 非空、回答数字与工具返回一致
- **应用 API 算术一致**：summary 汇总 = 明细逐条加总
- **写操作全流程**：POST 真实数据 → 查 summary 确认联动 → 重启应用还原种子数据
- **应用侧 AI 代理链路**：`POST /api/assistant/stream` 流式输出非空
- **UI 可用性**：页面各区块（图表/筛选/表单/AI 面板）逐一过一遍，确认无 JS 报错、数据渲染正确
- 任一项失败 → 先修再继续，禁止"基本可用"就交付

**部署形态（交付前确定，二选一）**：
- **默认：本地起服务验证**——DSH 8090 + 应用 `java -jar --server.port=18081`（显式指定端口），本地完成全链路验证后再谈交付
- **用户要云服务器部署**——构建产物验证通过后用 `bash <skill_path>/scripts/deploy_remote.sh -t user@host -a <应用jar> [-g <插件jar>] [-s skip]` 一键部署（上传 JAR → 生成远端 restart_all.sh → 启动 → 探活）；部署后注意：① 云安全组放行 8090 与应用端口 ② 插件需重新 install+activate（指向远端 DSH）③ 交付地址换成公网 IP，生命周期说明里写明重启命令 `ssh <host> 'bash /opt/dsh/restart_all.sh'` ④ 云部署后全部验证项（逐工具实测/UI/探活）必须在远端地址上重跑一遍，本地验证通过 ≠ 远端可用

**交付前地址探活（必须刚刚执行过才算数）**：对每个要交给用户的地址跑 `curl --noproxy '*' -o /dev/null -w "%{http_code}"`，确认 HTTP 200；进程被回收就重新拉起再交付。禁止把历史地址或"应该能访问"的地址写给用户。

- 交付时必须给出：
  - **确实可访问的地址**（刚探活确认）：DSH `http://<host>:8090`（默认模型渠道已配置，可直接对话）+ 应用 `http://<host>:<port>`
  - **体验流程说明**（按 `references/readme-delivery-template.md` 的「体验流程说明」节写，给使用者照做就能体验到完整闭环）：① 打开应用能看到什么 ② AI 面板在哪、逐条示例问题（注明触发哪个工具）③ DSH 控制台示例话术 ④ 写操作如何体验（AI 会先确认）
  - 插件工具清单（`plugin__<pluginId>__<tool>` 形式）与验证方法
  - **服务生命周期说明（必给）**：告知服务跑在哪个机器/哪个进程上、长驻进程可能被回收；访问 404 时对助手说一句「重启服务」即可拉起（或给出明确的重启命令行）；内存型预置数据重启后自动还原，写操作产生的数据不保留
- **交付前自检**：过 `references/prompt-recipes.md`「细腻度验收清单」7 项（数据密度/数据故事/零占位文案/数字算术/状态机/AI 有据/移动端），任一不过先修再交付
- **最终交付清单（收尾必做）**：跑 `bash <skill_path>/scripts/delivery_check.sh <pluginId>`（探活/插件状态/鉴权回归自动项）→ 逐条过 `references/delivery-checklist.md` 手工项（**UI 必须真实浏览器点击验证**：弹层/抽屉开关、表单提交、toast——curl 测不出"弹层关不掉"这类 bug；改过全局机制必跑回归矩阵）→ 清单全勾才算交付

### 阶段 5：文档沉淀（必做，交付的一部分）
开发验证完成后，**必须按 `references/readme-delivery-template.md` 产出/完善工程 README**，包含四块：
1. **README 本体**：使用说明、快捷体验流程、一句话需求、服务地址（探活后的真实地址）、插件信息、工具清单、体验流程说明、预置数据说明、构建与启动、环境坑位记录
2. **简历项目模板**：STAR 3~5 行，数字具体、机制写清（模板见 reference）
3. **技术关键词**：按真实使用情况勾选（Java 17 / Spring Boot 3.x / SPI / Function Calling / SSE / 插件生命周期…）
4. **面试重点**：7 个高频追问 + 答题要点（模板已含，深度追问兜底 `references/interview-notes.md`）

**README 截图要求**：截图不得重复；必须至少包含一张真实 AI 对话截图（能看到用户问题、模型回答或工具结果卡片）。

**GitHub 仓库元数据**：About 描述必须用中文，写清业务场景与 AI 能力；仓库 topics 必须包含 `dsh-java` 与 `dsh-java-plugin`，可按项目补充业务领域标签（如 `spring-boot`、`ai-agent`、`mall`）。

其他附加资料按需：架构图 → `references/architecture.md`（含 Mermaid）。

### 阶段 6：验收闭环与迭代（交付≠结束，链路不是直线）

**验收确认（交付后必做）**：交付完必须向用户索要验收反馈（「打开两个地址过一遍体验流程，有要调的告诉我」），用户明确验收通过前任务不算完结；交付完不追问就收工是违规。

**迭代循环（用户反馈修改时）**：
- **小改动**（文案/预置数据/单个工具逻辑）：改完只重跑受影响工具的 `agent_stream.sh` 实测 + 地址探活 + UI 触及区块过一遍 → 重新交付，不必全量回归
- **大改动**（新增工具/改数据模型/改全局机制如 Filter、拦截器）：重走阶段 3 设计确认 + 全链路验证 + `delivery_check.sh`，改全局机制必跑回归矩阵
- 每次迭代同步更新 README 的工具清单与体验流程说明，禁止 README 与实际能力脱节

**断点续作（跨会话恢复，用户说「继续/接着做」时）**：禁止从头重做，先做三件事再动手：
1. **探活**：`curl --noproxy '*'` 探 DSH 8090 与应用端口，进程被回收就重新拉起
2. **盘点**：查工程目录（app/plugin 模块、构建产物）、DSH 插件安装激活状态，对照本流程判断卡在哪个阶段
3. **汇报**：向用户报当前进度（已完成什么/卡在哪/还剩什么）+ 下一步计划，然后继续执行

## Gotchas

- 交付形态问法必须用阶段 0「第二问」的固定措辞，禁止出现「独立可运行项目」「插件交付」这类不带解释的裸选项——用户会误以为插件模式「不对接应用」、独立模式是更高配
- 插件依赖 scope 必须 `provided`，否则 fat jar 与宿主类冲突加载失败
- `plugin.yaml` 的 `entrypoint` 是**插件主类全限定名**，而 install 接口的 `entrypoint` 字段是 **JAR 文件名**，两者不同
- install 接口 `sourcePath` 必须是宿主可访问的**绝对路径**
- 修改插件配置（如 mall.service-token）后需停用再启用插件，`configure()` 才会重新执行
- 工具 description 直接影响 Agent 调用准确性：写清「何时必须调用、何时不要调用、返回什么」
- 插件不直连数据库等敏感资源，通过业务应用 Admin API 走 HTTP，守住安全边界
- DSH 未配置模型时对话报错，先检查「设置 → 模型设置」
- 端口约定：DSH 8090；案例应用 18080（商城）/ 8091（MySQL 平台），新应用避开这些端口
- 新应用前端必须先读 `references/ui-design-guide.md` 并按其自检"AI 味清单"，出现 AI 味信号（默认蓝紫渐变/emoji 图标/裸表格/无 hover 过渡）即为不合格
- **禁止千篇一律的 UI**：每类场景必须用专属设计语言（ui-design-guide.md 1.5 分域设计语言表）——写码前先产出「设计语言卡」（主色/背景材质/形状母题/标志性元素/气质关键词 3 个）；**并按硬规则 5 与库内最邻近 2~3 个案例逐一对比**（同板块优先，其次邻近板块），差异点（辅助色/材质/布局/母题至少两项）写进设计语言卡并随 README 交付；页面要有贯穿全页的标志性视觉母题，AI 面板配色跟随领域风格。打开页面 3 秒说不出"这是哪类产品"，或"换个主色就是新应用"，均为不合格
- **AI 气泡禁止 `textContent` 裸显模型回复**：模型输出是 markdown（`**加粗**`/`- 列表`/`###`），必须用 `renderMd` 渲染（代码见 ui-design-guide.md 第五节），流式期间逐 chunk 实时 `innerHTML` 重渲染；先 `escapeHtml` 防 XSS，用户气泡保持 `textContent`。交付清单 UI 层有对应检查项
- **视觉/拍照类场景（P100~P104）必须按「视觉场景实现规范」实现**（prompt-recipes.md 视觉智能节末）：图像走"上传→image_id→工具入参"链路，工具 schema 禁止 base64；识别走预置样张库（默认）或应用侧视觉模型 API（增强），识别结果必须带置信度；卡路里/估价等数值一律来自预置库 × 分量，禁止 AI 现编；页面固定位置展示免责声明（估算仅供参考/民俗观点仅供娱乐/以线下定损为准）
- **视觉/拍照类场景（P100~P104）必须按「视觉场景实现规范」实现**（prompt-recipes.md 视觉智能节末）：图像走"上传→image_id→工具入参"链路，工具 schema 禁止 base64；识别走预置样张库（默认）或应用侧视觉模型 API（增强），识别结果必须带置信度；卡路里/估价等数值一律来自预置库 × 分量，禁止 AI 现编；页面固定位置展示免责声明（估算仅供参考/民俗观点仅供娱乐/以线下定损为准）

### 运行环境坑位（沙箱/受限代理环境必看，详见 runtime-pitfalls.md）
- **SERVER_PORT 环境变量劫持**：沙箱注入 `SERVER_PORT=<随机端口>`，Spring relaxed binding 优先级高于 yml，应用会绑错端口。启动一律显式 `java -jar app.jar --server.port=18081`
- **HTTP_PROXY 导致本机 curl 502**：本机 curl 502/000 ≠ 服务挂了，先 `lsof -nP -iTCP:<port> -sTCP:LISTEN` 确认监听；curl 统一加 `--noproxy '*'` 或 `export no_proxy=127.0.0.1,localhost`
- **nohup/脚本子进程会被会话回收**：长驻服务用受管后台任务启动；每次交付/回访前先探活，进程没了重新拉起
- **agentId 无需预置**：DSH 按需创建 agent，任意 agentId 直接用于 /api/agent/stream 端到端测试
- **runtime jar 已带默认模型渠道**：技能包内 runtime jar 启动后即可端到端验证，不配模型也能通
- **内存种子数据**：写操作测完重启应用即还原，无需回滚脚本

### DSH 已知问题（详见 delivery-checklist.md 第三节）
- 流式请求被强断后 Agent 卡 RUNNING（后续请求报「正在执行上一条消息」），无 stop 端点，只能重启 DSH；自动化中不要强杀流式请求进程
- step_break 事件会重复推送两次（单次执行只一条 tool_result，未重复执行），展示层需去重
- **改全局机制（Filter/拦截器/统一异常处理）必须跑回归矩阵**：新规则生效 + 每条既有链路（页面流、AI 代理 SSE、Agent 端到端）完好，青苔记账曾因全局 token Filter 误伤 AI 代理导致「无法连接 AI 财务助手」
