# 微网站自动化工作流

> 每日云端自动产出 1 个海外轻量级网站,通过 AdSense 获利的完整流水线文档。

---

## 一、整体架构

```
┌─────────────────────────────────────────────────────────────┐
│                       TRAE 云端调度                          │
│  ┌──────────────────────┐    ┌──────────────────────┐         │
│  │ 每日微网站生产        │    │ 每周六站点复盘        │         │
│  │ 每天 09:15 (北京时间) │    │ 每周六 20:00 (北京)   │         │
│  └──────────┬───────────┘    └──────────┬───────────┘     │
└─────────────┼──────────────────────────────┼─────────────────┘
              │                              │
              ▼                              ▼
       ┌─────────────────────────────────────────┐
       │       GitHub (Max179 账号)              │
       │  ┌────────────────────────────────┐     │
       │  │ micro-sites-hub (中心仓库)      │     │
       │  │  - templates/    模板系统         │     │
       │  │  - live-log.md   每日日志         │     │
       │  │  - sites/        推广文案         │     │
       │  │  - reviews/      复盘报告         │     │
       │  │  - AUTOMATION-WORKFLOW.md (本文档)│     │
       │  └────────────────────────────────┘     │
       │  ┌────────────────────────────────┐     │
       │  │ site-YYYYMMDD-{topic}          │     │
       │  │  (每天新建的独立站点仓库)        │     │
       │  └────────────────────────────────┘     │
       └─────────────────────────────────────────┘
              │
              ▼
       ┌─────────────────────────────────────────┐
       │   GitHub Pages 自动部署                  │
       │   https://max179.github.io/{repo-name}/  │
       └─────────────────────────────────────────┘
```

## 二、仓库结构

### 1. 中心仓库: `Max179/micro-sites-hub`

存放所有共享资源,所有任务读写都通过这个仓库。

```
micro-sites-hub/
├── templates/                 # 模板系统
│   ├── tool/                  # 工具站模板
│   │   └── index.html
│   ├── info/                  # 资讯站模板
│   │   └── index.html
│   ├── calculator/            # 计算站模板
│   │   └── index.html
│   └── shared/                # 共享基建(所有站点共用)
│       ├── seo.js             # SEO + JSON-LD + sitemap
│       ├── analytics.js       # GA4 + 按钮点击追踪
│       ├── ads.js             # 3 个标准广告位
│       ├── base.css           # 响应式 + 暗色模式基础样式
│       └── config.json        # 全局配置 (siteUrl / gaId / adsenseId)
├── live-log.md                # 每日产出日志(任务自动追加)
├── sites/                     # 推广文案存档
│   └── YYYYMMDD/
│       └── promotion-copy.md  # 当日各社区推广文案
├── reviews/                   # 每周复盘报告
│   └── YYYYMMDD-weekly-review.md
└── AUTOMATION-WORKFLOW.md     # 本文档
```

### 2. 每日新站仓库: `Max179/site-YYYYMMDD-{topic-slug}`

每天 1 个独立仓库,包含完整站点代码。

```
site-20260703-caffeine-half-life-calculator/
├── index.html                 # 填充后的页面
├── shared/                    # 从 hub 复制过来的共享基建
│   ├── seo.js
│   ├── analytics.js
│   ├── ads.js
│   ├── base.css
│   └── config.json            # siteUrl 已替换为该站 URL
├── sitemap.xml
└── robots.txt
```

## 三、模板系统

### 三种站点类型

| 类型 | 适用场景 | 文案语气 | 必填占位符 |
|------|---------|---------|------------|
| `tool` | 需要交互操作的工具 | 营销语气 | 15 个 |
| `info` | 信息汇总资讯站 | 客观陈述 | 9 个 |
| `calculator` | 数值计算 | 客观清晰 | 12 个 |

### 通用占位符

```
{{SITE_TITLE}}        站点标题
{{META_DESCRIPTION}}   SEO 描述
{{KEYWORDS}}           关键词(逗号分隔)
{{CANONICAL_URL}}      规范化 URL
{{YEAR}}               当前年份
```

### 共享基建(已预制,无需每次重做)

- **SEO**: `seo.js` 自动生成 meta / JSON-LD / sitemap
- **埋点**: `analytics.js` 预置 GA4,自动追踪 `[data-track]` 属性的按钮点击
- **广告位**: `ads.js` 预留 3 个标准位置(header / incontent / sidebar),初期隐藏,流量达标后改 `config.json` 一键开启

### 图表扩展能力(按需启用)

默认 3 种模板都是**纯 HTML + 原生 JS**,无构建步骤,直接部署 GitHub Pages。
当站点的痛点本身需要数据可视化时(如趋势对比、占比分布、多维数据),应升级该站为 React 项目,并强制遵循以下 UI 规范:

- **图表组件**: 必须使用 bklit-ui(见附录 A)
- **其他所有 UI 组件**: 必须使用 HeroUI(见附录 B)
- **禁止混用**: 不允许引入 MUI / Chakra / shadcn / Mantine 等其他 React UI 库

> 当前默认模板不内置 React 依赖,仅在任务识别到图表需求时按附录 A/B 升级该站。

### 配置文件 `config.json`

```json
{
  "siteName": "",
  "siteUrl": "",           // 每站自动替换
  "gaId": "",              // 待填入实际 GA ID
  "adsenseId": "",         // 待填入实际 AdSense ID
  "ads": { "enabled": false }
}
```

> ⚠️ `gaId` 和 `adsenseId` 当前为占位符,获取后只需更新 `micro-sites-hub/templates/shared/config.json` 即可,所有站点自动生效。

## 四、自动化任务

### 任务 1: 每日微网站生产

| 字段 | 值 |
|------|-----|
| **任务 ID** | `767fecac` |
| **调度** | 每天 09:15 (北京时间) |
| **执行环境** | TRAE 云端新会话(完全云端,不依赖本地) |
| **状态** | Active |

#### 执行流程

```
第 0 步: 幂等检查
  └─ 读取 hub 仓库 live-log.md,如已有今日成功记录则跳过

第 1 步: 挖掘痛点
  └─ WebSearch 扫描 Reddit / Product Hunt / X
     筛选标准: 单点问题 / 可快速解决 / 有搜索量 / 无强势竞品
     记录痛点描述、目标用户、关键词,并标记是否需要图表(见附录 A)

第 2 步: 选模板 + 填内容
  └─ 判断类型(tool / info / calculator)
     从 hub 仓库 get_file_contents 读取模板
     替换所有 {{PLACEHOLDER}} 占位符
     如痛点标记为“需要图表”,按附录 A + B 升级为 React 项目

第 3 步: 部署到 GitHub Pages
  ├─ create_repository 创建新仓库 site-YYYYMMDD-{slug}
  ├─ push_files 推送文件
  │  (纯 HTML: index.html + shared/* + sitemap.xml + robots.txt)
  │  (React 项目: src/、package.json、vite.config.ts、
  │   .github/workflows/deploy.yml 详见附录 B)
  ├─ 调用 GitHub Pages API 自动开启 Pages
  │  (React 项目改用 source: GitHub Actions)
  └─ WebFetch 验证 URL 可访问(允许 Pages 生效延迟)

第 4 步: 生成推广文案
  └─ 为 Reddit / X / Indie Hackers / Hacker News 生成文案
     推送到 hub 仓库 sites/YYYYMMDD/promotion-copy.md
     (只生成文案不自动发帖,需手动发布)

第 5 步: 更新日志
  └─ 追加一行到 hub 仓库 live-log.md
```

#### live-log.md 字段

| 日期 | 站点名称 | 类型 | GitHub仓库 | 线上URL | 痛点 | 关键词 | 状态 |
|------|---------|------|-----------|---------|------|--------|------|

### 任务 2: 每周六站点复盘

| 字段 | 值 |
|------|-----|
| **任务 ID** | `77da2848` |
| **调度** | 每周六 20:00 (北京时间) |
| **执行环境** | TRAE 云端新会话 |
| **状态** | Active |

#### 执行流程

```
第 1 步: 收集本周站点
  └─ 从 hub 仓库读取 live-log.md,解析本周所有站点

第 2 步: 检查各站状态
  ├─ list_commits 检查仓库最近提交
  └─ WebFetch 检查线上 URL 可访问性

第 3 步: 生成复盘报告
  └─ 推送到 hub 仓库 reviews/YYYYMMDD-weekly-review.md
     内容包含:
     - 本周站点概览表
     - 需手动检查的 GA 数据清单
     - 优化建议(分好/差/暂停三类)
     - 下周优先方向

第 4 步: 更新日志
  └─ 追加复盘记录到 live-log.md
```

## 五、部署流程

### 自动化部分(任务自动完成)

1. **创建仓库**: `create_repository` (name, private=false, autoInit=true)
2. **推送代码**: `push_files` (index.html + shared/* + sitemap.xml + robots.txt)
3. **开启 Pages**: `POST /repos/Max179/{repo}/pages` (source: main / root)
4. **验证访问**: `WebFetch` 请求线上 URL,允许首次 404 (Pages 构建需 1-2 分钟)

### 手动部分(可选,任务自动跳过)

- **提交搜索引擎收录**: 需手动到 Google Search Console / Bing Webmaster Tools 提交
- **推广发帖**: 推广文案已生成到 `sites/YYYYMMDD/promotion-copy.md`,需手动复制到各社区发布

## 六、维护说明

### 日常监控

- **每日 09:15 后**: 查看云端任务执行结果
- **每周六 20:00 后**: 查看复盘报告,根据建议决定下周优化方向
- **随时**: 访问 https://github.com/Max179?tab=repositories 查看所有站点仓库

### 配置更新

| 需求 | 操作 |
|------|------|
| 更新 GA ID | 改 `templates/shared/config.json` 的 `gaId` 字段 |
| 更新 AdSense ID | 改 `templates/shared/config.json` 的 `adsenseId` 字段 |
| 开启广告 | 改 `config.json` 的 `ads.enabled` 为 `true` |
| 暂停每日任务 | `Schedule action: pause` 任务 `767fecac` |
| 暂停复盘任务 | `Schedule action: pause` 任务 `77da2848` |

### Token 权限要求

当前使用的 GitHub Classic Token 需要 `repo` scope,涵盖:
- 创建仓库
- 推送文件
- 开启 Pages
- 查询 commits

### 失败排查

| 症状 | 可能原因 | 解决方法 |
|------|---------|----------|
| 任务未触发 | 任务被 pause | `Schedule action: resume` |
| 创建仓库 403 | Token 权限不足 | 重新生成带 `repo` scope 的 Classic Token,更新 mcp.json |
| Pages 404 | 构建未完成 | 等 1-2 分钟,或手动触发 `POST /repos/{owner}/{repo}/pages/builds` |
| 模板读取失败 | hub 仓库结构变动 | 检查 `templates/{type}/index.html` 路径是否正确 |
| 推广文案未生成 | 第 4 步出错 | 查看任务执行记录,手动补写 |
| React 站点 Pages 构建失败 | workflow 文件错误 | 检查 `.github/workflows/deploy.yml`,确认 Node 版本与 build 脚本 |
| React 项目依赖冲突 | 引入了非 heroui/bklit-ui 的 UI 库 | 检查 package.json,仅保留 @heroui/* 与 bklit-ui 相关依赖 |

## 七、原则与边界

### 原则

1. **先跑通流水线,不追求完美** — 每天推进一个项目,不贪多
2. **完全云端执行** — 不依赖本地文件系统,所有数据托管到 GitHub
3. **执行到成功为止** — 遇到错误先排查修复,不中途放弃
4. **幂等性** — 当日已成功则跳过,不重复执行
5. **数据可追溯** — 所有变更通过 git commit 记录,可版本回溯
6. **UI 统一规范** — React 项目的 UI 组件只能用 HeroUI,图表只能用 bklit-ui,禁止混用其他库

### 不做的事

- ❌ 自动发帖推广(只生成文案,需人工审核发布)
- ❌ 自动获取 GA 数据(只生成检查清单,需人工登录查看)
- ❌ 自动提交搜索引擎收录(需人工到 Search Console 操作)
- ❌ 编造数据(复盘报告只列指标项,不填假数据)
- ❌ 在 React 项目中引入 MUI / Chakra / shadcn / Mantine 等其他 UI 库

## 八、当前状态

### 已完成

- [x] 模板系统(3 种类型 + 共享基建)已推送到 `micro-sites-hub`
- [x] 每日生产任务 `767fecac` 已创建并云端化
- [x] 每周复盘任务 `77da2848` 已创建并云端化
- [x] 首次执行已产出 2 个站点:
  - `site-20260703-caffeine-half-life-calculator` ✅ 已上线
  - `site-20260703-reading-time-calculator` ✅ 已上线
- [x] bklit-ui 图表使用规范已写入附录 A(待痛点需要时启用)
- [x] HeroUI UI 组件规范已写入附录 B(强制约束)

### 待办

- [ ] 填入实际 GA ID 到 `templates/shared/config.json`
- [ ] 填入实际 AdSense ID 到 `templates/shared/config.json`(流量达标后)
- [ ] 提交站点到 Google Search Console 收录
- [ ] 发布每日推广文案到对应社区
- [ ] (可选)首次痛点需要图表时,按附录 A + B 升级站点为 React 项目

---

## 附录 A: bklit-ui 图表使用规范

### A.1 何时启用

当痛点本身需要**数据可视化**才能解决时启用。判断标准:

| 触发场景 | 示例痛点 | 是否启用 |
|---------|---------|----------|
| 需要展示时间趋势 | “加密货币价格走势可视化”、“用户增长曲线展示” | ✅ 启用(Line / Area / Live Line) |
| 需要展示占比分布 | “个人月度支出分类占比”、“设备市场份额分布” | ✅ 启用(Pie / Ring / Funnel) |
| 需要多维数据对比 | “技能雷达图生成”、“各维度性能对比” | ✅ 启用(Radar / Bar) |
| 需要地理区域数据 | “各国 GDP 对比可视化”、“区域销售热度图” | ✅ 启用(Choropleth) |
| 需要流程/流量分析 | “用户漏斗转化分析”、“能源流向分析” | ✅ 启用(Funnel / Sankey) |
| 需要进度/指标显示 | “年度目标完成率仪表盘” | ✅ 启用(Gauge / Ring) |
| 纯交互工具(无需图表) | “咖啡因半衰期计算”、“阅读时间估算” | ❌ 走纯 HTML 模板,不启用 |
| 纯信息汇总(无需图表) | “最佳咖啡豆品牌推荐” | ❌ 走纯 HTML 模板,不启用 |

> 决策原则:能不升级就不升级。纯 HTML 能表达的绝不引入 React。

### A.2 bklit-ui 简介

- **仓库**: https://github.com/bklit/bklit-ui
- **文档站**: https://ui.bklit.com
- **技术栈**: React + Visx + Motion + Tailwind 4
- **分发方式**: shadcn registry (`ui.bklit.com/r/*.json`)
- **许可**: 开源(见仓库 LICENSE)

### A.3 可用图表类型

| 图表 | 适用场景 | registry URL |
|------|---------|--------------|
| `area-chart` | 时间序列趋势(带填充) | `ui.bklit.com/r/area-chart.json` |
| `bar-chart` | 分类对比、堆叠、横向 | `ui.bklit.com/r/bar-chart.json` |
| `line-chart` | 时间序列趋势(折线) | `ui.bklit.com/r/line-chart.json` |
| `live-line-chart` | 实时流数据(股票/监控) | `ui.bklit.com/r/live-line-chart.json` |
| `pie-chart` | 占比分布(饼图/环图) | `ui.bklit.com/r/pie-chart.json` |
| `ring-chart` | 多环进度对比 | `ui.bklit.com/r/ring-chart.json` |
| `radar-chart` | 多维能力对比 | `ui.bklit.com/r/radar-chart.json` |
| `gauge-chart` | 单值进度仪表 | `ui.bklit.com/r/gauge-chart.json` |
| `funnel-chart` | 漏斗转化分析 | `ui.bklit.com/r/funnel-chart.json` |
| `sankey-chart` | 流量/能源流向 | `ui.bklit.com/r/sankey-chart.json` |
| `candlestick-chart` | OHLC 蜡烛图 | `ui.bklit.com/r/candlestick-chart.json` |
| `choropleth-chart` | 地理区域数据 | `ui.bklit.com/r/choropleth-chart.json` |
| `composed-chart` | 多系列混合(柱+线+面积) | `ui.bklit.com/r/composed-chart.json` |

### A.4 升级步骤(任务执行时)

当第 1 步识别痛点需要图表时,第 2 步按以下流程升级:

1. **项目脚手架**: 新建 React + Vite + TypeScript 项目结构(详见附录 B 第 B.4 节)
2. **拉取图表组件**: 通过 shadcn CLI 拉取需要的图表组件源码
   ```bash
   npx shadcn@latest add https://ui.bklit.com/r/{chart-name}.json
   ```
   源码会写入 `src/components/ui/{chart-name}.tsx`,直接打包进生产 bundle,无需外部 CDN。
3. **配套 UI 组件**: 表单 / 按钮 / 卡片 / 模态框等所有非图表 UI,必须从 HeroUI 拉取(见附录 B)。
4. **GitHub Actions 自动构建**: 推送 `.github/workflows/deploy.yml`(配置见附录 B 第 B.4 节)
5. **开启 Pages(source 改为 GitHub Actions)**:
   ```bash
   curl -X PUT \
     -H "Authorization: token $TOKEN" \
     -H "Accept: application/vnd.github+json" \
     https://api.github.com/repos/Max179/{repo}/pages \
     -d '{"build_type":"workflow"}'
   ```
6. **共享基建复用**: `public/shared/` 仍放 hub 仓库的 `seo.js / analytics.js / ads.js / base.css`,在 `index.html` 模板里引用,保证 SEO/埋点/广告位三件套不变。

### A.5 注意事项

- **构建慢**: React 项目首次 Actions 构建约 2-3 分钟,比纯 HTML 慢很多
- **Bundle 体积**: 仅拉取用到的图表组件,不要全量引入 bklit-ui
- **数据准备**: 图表需要结构化数据,任务生成时要同时生成示例数据集(至少 10 条)
- **SSR/SSG**: 当前用 Vite 纯 SPA,GitHub Actions 构建后静态部署即可,无需 Next.js
- **主题一致性**: bklit-ui 用 CSS 变量主题,与 HeroUI 的 Tailwind v4 主题系统协调(均基于 Tailwind 4)

### A.6 决策流程图

```
痛点识别
   │
   ▼
是否需要数据可视化?
   ├─ 否 → 走纯 HTML 模板 (tool/info/calculator)
   │
   └─ 是 → 选择图表类型
            │
            ▼
         升级为 React + Vite 项目
            │
            ▼
         拉取 bklit-ui 图表组件 (附录 A)
            +
         拉取 HeroUI UI 组件 (附录 B)
            │
            ▼
         推送代码 + GitHub Actions workflow
            │
            ▼
         开启 Pages (source: GitHub Actions)
            │
            ▼
         等待首次构建完成 (2-3 分钟)
```

---

## 附录 B: HeroUI UI 组件规范

### B.1 强制约束

**当站点升级为 React 项目时(即 needs_chart=true),所有非图表 UI 组件必须且只能使用 HeroUI。**

| 允许 | 用途 |
|------|------|
| ✅ `@heroui/react` 及子包 | 所有通用 UI(Button / Card / Modal / Select / Table / Form / Input / Nav / Toast 等) |
| ✅ bklit-ui(见附录 A) | 仅限图表与数据可视化组件 |
| ❌ MUI / Material UI | 禁止使用 |
| ❌ Chakra UI | 禁止使用 |
| ❌ shadcn/ui(非 bklit-ui registry 部分) | 禁止使用 |
| ❌ Mantine | 禁止使用 |
| ❌ Ant Design / antd | 禁止使用 |
| ❌ 自行手写 UI 组件 | 禁止使用(必须复用 HeroUI) |

> 此约束的目的是保持设计系统统一、降低维护成本、确保 a11y 一致性。

### B.2 HeroUI 简介

- **仓库**: https://github.com/heroui-inc/heroui
- **文档站**: https://heroui.com
- **Storybook**: https://storybook-v3.heroui.com/
- **前身**: NextUI(v3 起改名为 HeroUI)
- **技术栈**: React 19 + React Aria + Tailwind CSS v4
- **分发方式**: npm 包(`@heroui/react` 或按需子包 `@heroui/button`、`@heroui/modal` 等)
- **许可**: MIT

### B.3 关键特性(为什么选 HeroUI)

- **Accessible by default**: 基于 React Aria,WCAG 合规的键盘/焦点/屏幕阅读器行为
- **Tailwind v4 原生**: 无 CSS-in-JS 运行时,产物更小,构建更快
- **Compound components**: 组合式 API(`Card.Header`、`Card.Content`、`Select.Item`),不是深度嵌套的 props
- **零 Provider 包装**: 不需要 `<Provider>` wrapper(与 Chakra / MUI 不同)
- **AI 原生**: 提供 MCP server 与 `llms.txt`,LLM 可精准理解组件用法
- **生产级**: 深度用户使用,稳定可靠

### B.4 React 项目集成步骤(任务执行时)

#### B.4.1 项目脚手架

```
site-YYYYMMDD-{slug}/
├── src/
│   ├── App.tsx                  # 主组件(用 HeroUI 布局 + bklit-ui 图表)
│   ├── main.tsx                 # 入口
│   ├── components/
│   │   └── ui/                  # bklit-ui 图表组件源码(shadcn 拉取)
│   │       └── {chart-name}.tsx
│   ├── styles/
│   │   └── globals.css          # Tailwind v4 入口 + HeroUI 主题变量
│   └── data/
│       └── sample-data.ts       # 图表示例数据集(至少 10 条)
├── public/
│   └── shared/                 # 复用 hub 的 shared 基建(seo.js / analytics.js / ads.js / base.css / config.json)
├── package.json
├── vite.config.ts
├── tsconfig.json
├── tailwind.config.ts           # 仅扩展 HeroUI 主题,不自定义绕过 HeroUI
├── postcss.config.js
├── .github/workflows/deploy.yml
└── README.md
```

#### B.4.2 依赖配置 `package.json`

```json
{
  "dependencies": {
    "react": "^19.0.0",
    "react-dom": "^19.0.0",
    "@heroui/react": "latest",
    "framer-motion": "latest",
    "tailwindcss": "^4.0.0"
  },
  "devDependencies": {
    "vite": "^5.0.0",
    "@vitejs/plugin-react": "latest",
    "typescript": "^5.0.0",
    "@types/react": "^19.0.0",
    "@types/react-dom": "^19.0.0",
    "autoprefixer": "latest",
    "postcss": "latest"
  }
}
```

> ⚠️ 严禁在 dependencies 中出现 `@mui/*`、`@chakra-ui/*`、`@mantine/*`、`antd` 等其他 UI 库。

#### B.4.3 全局样式 `src/styles/globals.css`

```css
@import "tailwindcss";
@import "@heroui/react/styles";

/* 复用 hub 的暗色模式与基础变量 */
:root {
  --color-primary: #2563eb;
  --color-bg: #f7f8fa;
}
```

#### B.4.4 主组件示例 `src/App.tsx`

```tsx
import {Button, Card, CardHeader, CardBody, Input, Select, SelectItem}
  from '@heroui/react';
import {BarChart} from './components/ui/bar-chart';  // bklit-ui 拉取
import {sampleData} from './data/sample-data';

export default function App() {
  return (
    <div className="min-h-screen bg-[var(--color-bg)]">
      <Card className="max-w-2xl mx-auto mt-8">
        <CardHeader>站点标题</CardHeader>
        <CardBody>
          <BarChart data={sampleData} />
          <Button color="primary">操作</Button>
        </CardBody>
      </Card>
    </div>
  );
}
```

#### B.4.5 GitHub Actions `.github/workflows/deploy.yml`

```yaml
name: Deploy to GitHub Pages
on:
  push:
    branches: [main]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

### B.5 组件选用清单(参考)

根据站点类型推荐组合:

| 场景 | 推荐 HeroUI 组件 | 配套 bklit-ui 图表 |
|------|------------------|-------------------|
| 数据仪表盘 | Card / Table / Tabs / Chip | Line / Area / Bar |
| 财务计算器 | Input / Select / Button / Modal | Pie / Ring(占比展示) |
| 资讯站升级 | Card / Chip / Tabs / Skeleton | Bar / Funnel(分类对比) |
| 进度追踪 | Progress / CircularProgress / Badge | Gauge / Ring |
| 多维对比 | Table / Modal / Accordion | Radar / Composed |

完整组件列表见 https://heroui.com/docs/components

### B.6 主题与样式约束

- **禁止绕过 HeroUI 自行手写组件样式**:如需定制,通过 HeroUI 的 theme / class variants 扩展
- **暗色模式**:沿用 HeroUI 的 `dark` class 机制,与 hub `base.css` 协调
- **CSS 变量**:全局 CSS 变量保留在 `src/styles/globals.css`,供 HeroUI 与 bklit-ui 共享
- **响应式**:必须用 Tailwind v4 的 `sm:` / `md:` / `lg:` 断点,不要自写媒体查询

---

**最后更新**: 2026-07-03  
**文档版本**: v1.2 (新增附录 B: HeroUI 强制 UI 规范)  
**维护者**: 自动化任务 + 用户手动跟进