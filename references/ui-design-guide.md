# 应用 UI 设计指南（无 AI 味）

目标：生成的每个应用界面都像**认真做过的产品**，而不是"AI 生成的 demo 页"。本指南从 2d-weekend-mall 真实前端（`mall-app/src/main/resources/static/`）与社区优质实践（50projects50days、SpinKit 等）提炼，生成前端时**必须遵守**。

## 一、先定调，再写码（Design Tokens）

写任何 HTML 前先确定 tokens，全部用 CSS 变量集中管理：

```css
:root {
  /* 1. 配色：一个主色 + 一个强调色 + 中性色阶。禁止默认蓝紫渐变 (#6366f1/#8b5cf6) */
  --bg: #f7f5ef;            /* 页面底色：暖白/奶油，不用纯白 #fff */
  --card: #fffdf8;          /* 卡片底 */
  --ink: #22302f;           /* 正文：深墨绿/炭黑，不用纯黑 */
  --muted: #71807b;         /* 次要文字 */
  --primary: #0e847a;       /* 主色：按领域选（文旅=青绿，医疗=蓝绿，餐饮=橘红，工具=石墨蓝） */
  --accent: #f4693d;        /* 强调色：仅用于徽标/角标/强调，占比 <10% */
  --line: rgba(34,48,47,.09);
  --shadow: 0 22px 60px rgba(46,74,68,.12);

  /* 2. 圆角：大圆角是"产品感"的关键，别用 4px/8px 的小工程师圆角 */
  --r-sm: 12px; --r-md: 20px; --r-lg: 28px; --r-pill: 999px;

  /* 3. 字体栈：系统字体优先，中文必须带 PingFang SC / Microsoft YaHei */
  --font: -apple-system, BlinkMacSystemFont, "PingFang SC", "Microsoft YaHei", sans-serif;
}
```

**选主色的思路**：从业务联想到颜色——文旅/研学→青绿+暖橙、健康→薄荷绿、金融→深墨绿、餐饮→橘红、儿童→明黄。避开 AI 味最重的两个信号：默认紫、默认蓝紫渐变。

### 1.5 分域设计语言（硬性要求：每类场景必须有专属视觉个性，禁止千篇一律）

**背景教训**：按同一套 tokens 生成的应用多了以后，会出现"换汤不换药"——所有应用都是暖白底 + 同款圆角卡 + 同款渐变按钮，只是文字不同。**每类场景必须有自己的一套设计语言**，用户打开页面 3 秒内应该能感受到"这是哪类产品"。

**写码前先声明「设计语言卡」**（写进设计稿，与代码一起交付）：

```
领域板块：<如 出行物流>
主色/强调色：<如 藏青 #1c2b4a / 荧光橙 #ff7a1a>
背景材质：<如 冷灰蓝渐变 + 轨迹虚线纹理>
形状语言：<如 中圆角 14px、方向箭头母题、进度用航线点线>
标志性元素：<如 运单卡上的"轨迹进度条"（点线+节点圆点）>
气质关键词：<3 个，如 干练 / 在途感 / 秩序>
```

**分域设计语言参照表**（每个板块 = 一套完整个性，含主色/材质/形状/标志性元素）：

| 板块（代表案例） | 主色/强调 | 背景材质 | 形状与母题 | 标志性元素 |
|---|---|---|---|---|
| 电商零售（P1/P6/P9/P53/P54/P97） | 暖橘 #e8632c / 深可可 | 奶油白 + 促销斜纹光斑 | 胖圆角卡、价格签形状（缺角标签） | 价格大字 + 「省 ¥xx」角标 |
| 金融财务（P3/P4/P20/P45/P46/P65/P66/P92） | 深墨绿 #0c3d33 / 香槟金 | 米金细纹纸感，克制无光斑 | 小圆角、细金线分隔、衬线数字 | 数字卡片用「存折行」式上下细线 |
| 出行物流（P5/P43/P44/P61/P62/P64） | 藏青 #1c2b4a / 荧光橙 | 冷灰蓝 + 轨迹点线纹理 | 中圆角、箭头/航线母题 | 轨迹进度条（点线 + 节点圆点） |
| 生活服务（P48/P49/P50/P95/P96/P99） | 陶土 #b96a4b / 暖灰绿 | 暖灰 + 手作纸感颗粒 | 圆胖卡 24px+、圆润图标 | 服务进度用「打卡圆点」 |
| 医疗健康（P11/P12/P40/P68/P69/P70/P91） | 薄荷绿 #2fa48d / 云白蓝 | 云白大面积留白 + 柔光晕 | 极大圆角 28px+、无锐角 | 体征曲线用柔和渐变面积图 |
| 教育学习（P13/P14/P17/P35~P38/P71~P73/P94） | 青绿 #0e847a / 暖橙 | 暖白 + 彩色便签色块 | 双色拼贴、进度徽章、书本母题 | 学习进度「徽章/奖牌」体系 |
| 工程效能·运维（P27~P34/P74~P80/P85） | 石墨 #23272e / 信号三色 | 深色表头 + 浅灰工作区 | 直角偏多、信息密集、等宽数字 | 状态灯（绿/黄/红圆点）+ 等宽字体指标 |
| 内容社区（P10/P18/P21/P22/P24） | 杂志黑 #1a1a18 / 朱红 | 纸白 + 大图占位 | 衬线标题、编辑排版、大留白 | 「刊头」式栏目标题（细线+序号） |
| 政务公益（P16/P26/P81/P82/P83） | 稳重蓝 #1f4e8c / 暖金 | 浅灰白、端正对称 | 规整卡片、国徽式庄重线条 | 事项卡「流程步骤条」 |
| 行业纵深（P25/P47/P51/P84/P86~P88） | 工业蓝灰 #2c3e50 / 安全橙 | 深色顶栏 + 网格纸底 | 数据密集表格卡、硬朗边框 | 传感器读数「仪表盘弧」或状态方块热力 |
| 角色工作台（P39/P41/P42/P89/P90/P92） | 高级灰 #3d3a36 / 皮革棕 | 暖灰 + 皮革纹理暗纹 | 时间线/日程为主视觉、中圆角 | 「今日待办」时间轴贯穿首屏 |
| 视觉智能·拍照识别（P100~P104） | 暗夜黑 #17181c / 取景青 #35d9c3 | 深色取景器质感（四角暗角渐变）+ 细扫描线 | 取景框角标母题（卡片四角画 ⌐ 形角标）、快门圆钮 | 「扫描识别」动效（横线扫过 + 结果卡逐条浮现） |

**四条硬规则**：

1. **同类不撞脸**：同板块的两个案例，辅助色、背景材质、布局骨架至少有**两项不同**（如 P43 快递查件偏 C 端轨迹卡、P62 车队调度偏 B 端调度棋盘）。生成前回看案例库同类应用的设计语言卡。
2. **标志性元素必须有**：每个应用有一个贯穿全页的视觉母题（上表第四列或自创），出现在 hero、卡片、空态中，而不是只有换色的通用模板。
3. **AI 面板跟随领域**：浮动按钮渐变、气泡配色、思考动画都要用本应用的设计语言色，不许所有应用共用一套默认配色。
4. **气质关键词验收**：交付前自问"打开页面 3 秒能说出这是哪类产品吗"，说不出 → 重做配色与母题。
5. **逐案例邻近对比（写码前必做）**：新应用不只是"和同板块不一样"——动手前从案例库挑出**最邻近的 2~3 个案例**（同板块优先，其次邻近板块），逐一对比设计语言卡，明确列出「与 P-XX 的差异：辅助色 X / 材质 X / 布局 X / 母题 X（至少两项）」，写进设计语言卡；设计语言卡随 README 交付（用户可查证）。案例库已有 100+ 应用，任何"换个主色就是新应用"的生成都是不合格。

## 二、"无 AI 味"的核心手法

1. **有层次的背景**：纯色背景 = demo 感。用「径向渐变光斑 + 细点阵纹理」：

```css
.page-bg {
  position: fixed; z-index: -2; inset: 0;
  background: radial-gradient(circle at 78% 0, #d9f3e8 0, transparent 34%),
              radial-gradient(circle at 8% 28%, #ffeeda 0, transparent 28%),
              linear-gradient(180deg, #fffdf8, var(--bg) 54%);
}
/* 细点阵，让背景有"材质" */
.page-bg::after {
  content: ""; position: fixed; z-index: -1; inset: 0;
  background-image: radial-gradient(rgba(14,132,122,.12) 1px, transparent 1px);
  background-size: 22px 22px; opacity: .28;
}
```

2. **Hero 区有排版张力**：大标题 `clamp(40px, 6vw, 72px)` + 负字距（`letter-spacing: -2px`）+ 一个彩色强调词（`<em>`）；小标签用大写字距 eyebrow（`letter-spacing: 3px; text-transform: uppercase; font-size: 12px`）
3. **卡片悬浮反馈**：hover 上浮 + 阴影加深（`translateY(-6px)`），transition 0.25s
4. **胶囊按钮**：`border-radius: var(--r-pill)`，主按钮带品牌色渐变 + 彩色投影（`box-shadow: 0 14px 30px rgba(primary,.22)`），hover 上浮 2px
5. **毛玻璃吸顶导航**：`position: sticky; backdrop-filter: blur(20px); background: rgba(card,.88); border-bottom: 1px solid var(--line)`
6. **数字有分量**：统计数字 26px+ 加粗，标签 13px muted，形成对比

## 三、AI 味清单（出现任何一条即为不合格，必须重写）

| AI 味信号 | 正确做法 |
| --- | --- |
| 默认蓝紫渐变 hero、`linear-gradient(135deg,#667eea,#764ba2)` | 按领域选主色，渐变仅用于主按钮/品牌标 |
| 纯白背景 + 居中一列卡片 | 渐变光斑背景 + 非对称布局（hero 左文右卡） |
| 所有圆角 8px、所有阴影 0 2px 4px | 大圆角 20~34px、多层大投影 |
| Inter 字体 + 全英文占位（Lorem ipsum） | 系统字体栈 + 真实中文业务文案 |
| emoji 当图标（🛒🚀✨） | 内联 SVG 图标（线性，1.5~2px stroke，`currentColor`） |
| 表格裸奔、灰色边框直角框 | 卡片式数据展示 + 斑马纹/悬浮高亮 |
| 按钮无 hover/无过渡、无加载态 | 所有交互元素有 transition + loading 态 |
| 三等分"feature 卡片"阵列（Why choose us x3） | 真实业务区块：列表/详情/表单/状态，信息密度有变化 |
| alert() 弹提示 | toast 通知（右上角滑入，2.5s 自动消失） |
| 与案例库其他应用共用同一套配色/卡片/母题（换汤不换药） | 按「1.5 分域设计语言」选专属设计语言卡，有标志性元素，同类案例至少两项差异 |

## 四、必备交互细节

- **加载态**：请求中按钮置灰 + spinner（内联 SVG 或纯 CSS 脉冲点）；列表加载用骨架屏（灰块 + shimmer 动画）
- **空态**：不是一片空白——给一句人话（"还没有报名记录，去看看路线吧"）+ 一个行动按钮
- **Toast**：成功绿/失败红，右上角滑入动画（`transform: translateX(120%) → 0`）
- **表单**：label 常显（不用 placeholder 当 label）、聚焦主色描边、错误内联红字
- **显隐切换**：用 `hidden` 属性控显隐时，若元素 CSS 写死了 `display`（如 `display:flex`），会**覆盖浏览器对 `hidden` 的默认 `display:none`**，导致"关不掉"。必须全局兜底 `[hidden] { display: none !important; }`（青苔记账弹层曾因此关不掉）
- **弹层/抽屉**：必须同时支持关闭按钮、遮罩点击、Esc 三种关闭方式；提交成功后自动关闭
- **响应式**：`clamp()` 字号、`grid-template-columns: repeat(auto-fill, minmax(280px, 1fr))`、移动端单列
- **微交互**：hover 上浮/变色、按下 `scale(.98)`、数字变化用滚动动画（参考 50projects50days 的 Incrementing Counter）

## 五、AI 助手面板（本技能应用的标配组件）

每个应用页面的 AI 入口统一做成**右下角浮动按钮 + 侧滑面板**（或 hero 区快捷卡）：

- 浮动按钮：56px 圆形，主色渐变 + 彩色投影，hover 轻微放大
- 面板：固定右侧 380px 宽，圆角 24px，毛玻璃底
- 消息气泡：用户消息主色底白字右对齐；AI 消息白底卡片左对齐
- **流式渲染**：读 DSH `/api/agent/stream` SSE，逐字追加；AI 思考中显示三点脉冲动画
- 工具调用过程可见：AI 消息中显示小标签「🔍 正在查询路线库…」增强真实感

### AI 回复必须做 Markdown 渲染（硬性要求，禁止裸文本）

**背景教训（外卖案例实测踩坑）**：模型回复默认输出 markdown（`**加粗**`、`- 列表`、`###` 小节）。AI 气泡若用 `textContent` 直接塞原文，用户看到的就是满屏星号和横杠星号列表，产品感尽毁。**AI 气泡一律走 md 渲染，且流式期间每个 chunk 实时重渲染**，用户看到的是逐步成型的排版而不是半截星号。

标准实现（轻量渲染器，零依赖，直接抄进生成的前端）：

```javascript
// ---- 轻量 Markdown 渲染（AI 回复气泡专用，零依赖） ----
function escapeHtml(s) {
  return s.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;');
}
function inlineMd(s) {
  return s
    .replace(/`([^`]+)`/g, '<code>$1</code>')
    .replace(/\*\*([^*]+)\*\*/g, '<strong>$1</strong>')
    .replace(/(^|[^*])\*([^*\n]+)\*/g, '$1<em>$2</em>')
    .replace(/\[([^\]]+)\]\((https?:[^)\s]+)\)/g, '<a href="$2" target="_blank" rel="noopener">$1</a>');
}
function renderMd(src) {
  const lines = escapeHtml(src).split('\n');
  let html = '', listBuf = [];
  const flushList = () => {
    if (listBuf.length) {
      html += '<ul>' + listBuf.map(i => '<li>' + inlineMd(i) + '</li>').join('') + '</ul>';
      listBuf = [];
    }
  };
  for (const raw of lines) {
    const line = raw.replace(/\s+$/, '');
    const li = line.match(/^\s*[-*•]\s+(.*)$/);
    if (li) { listBuf.push(li[1]); continue; }
    flushList();
    if (!line.trim()) continue;
    const h = line.match(/^(#{1,4})\s+(.*)$/);
    if (h) { const lv = Math.min(h[1].length + 2, 5); html += `<h${lv}>` + inlineMd(h[2]) + `</h${lv}>`; continue; }
    html += '<p>' + inlineMd(line.trim()) + '</p>';
  }
  flushList();
  return html || '<p></p>';
}
```

配套 CSS（AI 气泡关掉 `pre-wrap`，md 元素给紧凑间距）：

```css
.msg.ai .bubble { white-space: pre-wrap; }      /* 普通兜底文本 */
.msg.ai .bubble.md { white-space: normal; }      /* md 渲染态 */
.md p { margin: 0 0 8px; }  .md > :last-child { margin-bottom: 0; }
.md ul, .md ol { margin: 2px 0 8px; padding-left: 20px; }
.md li { margin: 3px 0; }
.md h3, .md h4, .md h5 { margin: 10px 0 4px; font-weight: 600; }
.md strong { font-weight: 600; }
.md code { background: rgba(0,0,0,.07); padding: 1px 5px; border-radius: 4px; font-size: 12px; }
```

流式接入方式（每个 chunk 重渲染，不要等结束）：

```javascript
// SSE chunk 累积到 answer 后：
if (answer) { bubble.classList.add('md'); bubble.innerHTML = renderMd(answer); }
else bubble.textContent = '正在想…';
```

三条铁律：
1. **先 `escapeHtml` 再拼 HTML**（渲染器已内置），杜绝模型输出注入 XSS；用户气泡保持 `textContent`
2. **流式期间实时渲染**，不是结束后一次性渲染——这是"产品感"的一部分
3. 不引外部 CDN 库（marked 等），应用要离线可跑；上面 40 行渲染器覆盖模型输出 95% 的格式（标题/列表/加粗/代码/链接）

## 六、参考资源（生成前可抓取学习）

- 2d-weekend-mall 前端源码：`mall-app/src/main/resources/static/`（styles.css 362 行完整 tokens 实践，**首选参照**）
- dsh-java-mysql 前端源码：`dsh-java-mysql-app/src/main/resources/static/`（管理后台型布局）
- [50projects50days](https://github.com/bradtraversy/50projects50days)：50 个纯 HTML/CSS/JS 小组件（toast、骨架屏、计数器、表单动效），按需抄单个组件
- [SpinKit](https://github.com/tobiasahlin/SpinKit)：纯 CSS loading spinners，`--sk-size/--sk-color` 变量定制
- 布局心法参考 Refactoring UI 要点：层次靠字号/字重/颜色深浅而非线框；间距用 8 的倍数；亲密性分组
