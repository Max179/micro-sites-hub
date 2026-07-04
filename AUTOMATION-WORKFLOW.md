# 微网站自动化工作流

> 每日云端自动产出 1 个海外轻量级网站,通过 AdSense 获利的完整流水线文档。

---

## 文档版本 v1.3 (2026-07-03)

**v1.3 变更**:
① 仓库全部改为私有(需 GitHub Pro 账号)
② SEO 长尾优化作为关键步骤(第 5.2 节)
③ SEO 主动提交流程(第 5.3 节)
④ HeroUI 优先 + 其他成熟开源库降级(附录 B.1)

## 一、整体架构

```
TRAE 云端调度
  ├─ 每日微网站生产 (ID 767fecac) - 每天 09:15 北京时间
  └─ 每周六站点复盘 (ID 77da2848) - 每周六 20:00 北京时间
        │
        ▼
GitHub (Max179 Pro 账号)
  ├─ micro-sites-hub (中心仓库, 私有) - 模板/日志/文案/复盘/本文档
  └─ site-YYYYMMDD-{topic} (每日新站, 私有) - 站点代码
        │
        ▼
GitHub Pages (Pro 账号支持私有仓库公开 Pages)
  https://max179.github.io/{repo-name}/
```

## 二、模板系统

### 三种站点类型

| 类型 | 适用场景 | 文案语气 |
|------|---------|----------|
| `tool` | 交互工具 | 营销 |
| `info` | 信息汇总 | 客观 |
| `calculator` | 数值计算 | 客观清晰 |

### 通用占位符

`{{SITE_TITLE}}` `{{META_DESCRIPTION}}` `{{KEYWORDS}}` `{{CANONICAL_URL}}` `{{YEAR}}`

### 共享基建(已预制)

- `seo.js`: SEO + JSON-LD + sitemap
- `analytics.js`: GA4 + 按钮点击追踪
- `ads.js`: 3 个标准广告位
- `base.css`: 响应式 + 暗色模式
- `config.json`: 全局配置

### React 升级触发条件

默认纯 HTML。痛点需要数据可视化时升级为 React 项目:
- 图表: 必须用 bklit-ui (附录 A)
- 其他 UI: 优先 HeroUI (附录 B)
- 禁止手写 UI 组件

## 三、自动化任务

### 任务 1: 每日微网站生产 (ID: 767fecac)

每天 09:15 (北京时间) 云端执行。

#### 执行流程

```
第 0 步: 幂等检查 - 读 live-log.md, 今日已成功则跳过

第 1 步: 挖掘痛点 + 长尾关键词矩阵 (详见第 4.1 节)

第 2 步: 选模板 + 填内容 + SEO 长尾优化 (详见第 4.2 节)
  - needs_chart=true 时按附录 A+B 升级为 React 项目

第 3 步: 部署到 GitHub Pages (仓库私有 + Pro 账号)
  - create_repository (private=true)  ← 必须私有
  - push_files
  - POST /repos/{owner}/{repo}/pages 开启 Pages
  - WebFetch 验证 URL (允许 Pages 生效延迟)

第 4 步: SEO 主动提交 (详见第 4.3 节)
  - Google ping / Bing ping / IndexNow

第 5 步: 生成推广文案 → 推送到 hub 仓库 sites/YYYYMMDD/promotion-copy.md
  - Reddit 标题用长尾问句型
  - X 含长尾词 hashtag

第 6 步: 更新日志 → 追加一行到 live-log.md
```

#### live-log.md 字段

| 日期 | 站点名称 | 类型 | GitHub仓库 | 线上URL | 痛点 | 关键词 | 状态 |

- 类型: React 项目标注 `{type}+chart`, 纯 HTML 标注原类型
- 关键词: 概要填核心 + 长尾数量, 如 `caffeine calculator [+12 长尾]`
- 状态: `已上线+已提交Google/Bing/IndexNow`

### 任务 2: 每周六站点复盘 (ID: 77da2848)

每周六 20:00 (北京时间) 云端执行。

1. 读 live-log.md 解析本周站点
2. list_commits + WebFetch 检查各站状态
3. 生成复盘报告 → reviews/YYYYMMDD-weekly-review.md
4. 追加复盘记录到 live-log.md

## 四、关键步骤详述

### 4.1 第 1 步: 挖掘痛点 + 长尾关键词矩阵

#### 痛点挖掘

WebSearch 搜索:
- `site:reddit.com {topic} problem OR issue OR frustrated OR wish there was`
- `site:producthunt.com {topic}`
- `{topic} tool OR calculator OR how to`

筛选标准: 单点问题、可快速解决、有搜索量、无强势竞品。

#### 长尾关键词挖掘

选定痛点后, WebSearch 做"长尾关键词挖掘":
- `{主关键词} how / what / why / best / vs / near / online / free`
- `{主关键词} reddit`、`how to {主关键词}`、`why does {主关键词}`
- 收集 10-15 个长尾变体, 覆盖问句型(how/why/what)、对比型(vs)、限定型(by/for/with)三类

记录: 痛点描述、目标用户、核心关键词(5-10 个) + 长尾关键词(10-15 个)、是否需要图表。

### 4.2 第 2 步: SEO 长尾优化(关键步骤, 吃长尾流量)

- **页面标题(title)**: ≤60 字符, 含主关键词 + 1 个高意图长尾词
  例: `Caffeine Half-Life Calculator | When Can I Sleep After Coffee`

- **meta description**: 150-160 字符, 自然嵌入 2-3 个长尾词, 带 CTA(`free`, `online`, `no sign-up`)

- **H1**: 1 个, 含主关键词

- **H2/H3**: 用长尾关键词作为子标题, 至少 3 个 H2
  例: `How long does caffeine stay in your system`

- **正文**: 自然分布长尾词 (密度 1-2%), 避免堆砌

- **FAQ 模块**: 至少 5 条, 每条 FAQ 用一个长尾问句作为 summary
  例: `Can I drink coffee at 4pm and still sleep at 11?`

- **JSON-LD**: FAQPage schema 含全部 FAQ 项; 工具站加 WebApplication schema, 资讯站加 Article schema, 计算站加 WebApplication + FAQPage

- **内部锚点**: hero CTA 链接到 #calc-section / #faq, FAQ 之间交叉链接

- **URL slug**: {repo-name} 用主关键词连字符形式

- **sitemap.xml**: 列出首页 + 各 H2 锚点 URL (#how-to-use, #faq 等)

- **robots.txt**: 允许全部, 指向 sitemap

### 4.3 第 4 步: SEO 主动提交(吃收录速度)

部署成功后, 主动让搜索引擎发现站点:

1. **Google ping**: WebFetch 访问
   `https://www.google.com/ping?sitemap=https://max179.github.io/{repo-name}/sitemap.xml`

2. **Bing ping**: WebFetch 访问
   `https://www.bing.com/ping?sitemap=https://max179.github.io/{repo-name}/sitemap.xml`

3. **IndexNow**: WebFetch POST 到
   `https://api.indexnow.org/indexnow`
   参数: `url={站点URL}&key={随机字符串}`
   Bing/Yandex 即时收录

4. 记录已提交的搜索引擎到 live-log.md 状态字段

> 注: 真正进入 Google 索引需用户在 Search Console 验证站点所有权, 自动化只能做 ping 通知。

## 五、部署流程

### 自动化部分

1. **创建仓库**: `create_repository` (name, **private=true**, autoInit=true)
2. **推送代码**: `push_files`
   - 纯 HTML: `index.html` + `shared/*` + `sitemap.xml` + `robots.txt`
   - React: `src/*` + `public/shared/*` + 配置文件 + `.github/workflows/deploy.yml`
3. **开启 Pages** (Pro 账号支持私有仓库公开 Pages):
   - 纯 HTML: `POST /repos/Max179/{repo}/pages` body `{"source":{"branch":"main","path":"/"}}`
   - React: `PUT /repos/Max179/{repo}/pages` body `{"build_type":"workflow"}`
4. **验证**: WebFetch 请求线上 URL, 允许 Pages 生效延迟

### 手动部分

- 提交搜索引擎收录: 自动化已 ping 通知
- 推广发帖: 推广文案已生成到 `sites/YYYYMMDD/promotion-copy.md`

## 六、维护说明

### 日常监控

- 每日 09:15 后: 查看云端任务执行结果
- 每周六 20:00 后: 查看复盘报告
- 随时: https://github.com/Max179?tab=repositories

### 配置更新

| 需求 | 操作 |
|------|------|
| 更新 GA ID | 改 `templates/shared/config.json` 的 `gaId` |
| 更新 AdSense ID | 改 `templates/shared/config.json` 的 `adsenseId` |
| 开启广告 | 改 `config.json` 的 `ads.enabled` 为 `true` |
| 暂停每日任务 | `Schedule action: pause` 任务 `767fecac` |
| 暂停复盘任务 | `Schedule action: pause` 任务 `77da2848` |

### Token 权限要求

GitHub Classic Token 需要 `repo` scope, 涵盖:
- 创建私有仓库
- 推送文件
- 开启 Pages
- 查询 commits

### 失败排查

| 症状 | 可能原因 | 解决方法 |
|------|---------|----------|
| 任务未触发 | 任务被 pause | `Schedule action: resume` |
| 创建仓库 403 | Token 权限不足 | 重新生成带 `repo` scope 的 Classic Token |
| Pages 404 (公开访问失败) | 账号未升级 Pro | 升级 GitHub Pro ($4/月) 解锁私有仓库公开 Pages |
| Pages 404 (构建未完成) | Pages 构建中 | 等 1-2 分钟, 或手动触发 `POST /repos/{owner}/{repo}/pages/builds` |
| 模板读取失败 | hub 仓库结构变动 | 检查 `templates/{type}/index.html` 路径 |
| 推广文案未生成 | 第 5 步出错 | 查看任务执行记录, 手动补写 |
| React 站点构建失败 | workflow 文件错误 | 检查 `.github/workflows/deploy.yml`, 确认 Node 版本与 build 脚本 |
| React 项目依赖冲突 | 引入了非 HeroUI/bklit-ui 的 UI 库 | 检查 package.json, 仅保留 @heroui/* 与 bklit-ui 相关依赖 |

## 七、原则与边界

### 原则

1. 先跑通流水线, 不追求完美
2. 完全云端执行, 不依赖本地文件系统
3. 执行到成功为止, 遇到错误先排查修复
4. 幂等性: 当日已成功则跳过
5. 数据可追溯: 所有变更通过 git commit 记录
6. **所有仓库必须私有 (private=true), 代码不公开, Pages 公网访问 (Pro 账号支持)**
7. **UI 强制规范**: React 项目的图表用 bklit-ui, 其他 UI 优先 HeroUI, HeroUI 缺失才用 shadcn/Radix/Headless UI 替代, 禁止手写 UI 组件
8. **SEO 长尾优化是关键步骤, 必须执行**: 标题/meta/H 标签/FAQ/sitemap/JSON-LD 全面覆盖长尾关键词

### 不做的事

- ❌ 自动发帖推广 (只生成文案, 需人工审核发布)
- ❌ 自动获取 GA 数据 (只生成检查清单, 需人工登录查看)
- ❌ 自动提交搜索引擎收录 (只 ping 通知, 需 Search Console 验证)
- ❌ 编造数据 (复盘报告只列指标项, 不填假数据)
- ❌ 在 React 项目中引入 MUI / Chakra / shadcn / Mantine / Ant Design 等其他 UI 库
- ❌ 创建公开仓库 (所有仓库必须私有)

## 八、当前状态

### 已完成

- [x] 模板系统 (3 种类型 + 共享基建) 已推送到 `micro-sites-hub`
- [x] 每日生产任务 `767fecac` 已创建并云端化
- [x] 每周复盘任务 `77da2848` 已创建并云端化
- [x] 首次执行已产出 2 个站点:
  - `site-20260703-caffeine-half-life-calculator` ✅ 已上线
  - `site-20260703-reading-time-calculator` ✅ 已上线
- [x] bklit-ui 图表使用规范 (附录 A)
- [x] HeroUI UI 组件规范 (附录 B)
- [x] SEO 长尾优化流程 (第 4.2 节)
- [x] SEO 主动提交流程 (第 4.3 节)
- [x] 仓库私有化约束 (需 Pro 账号)

### 待办

- [ ] 填入实际 GA ID 到 `templates/shared/config.json`
- [ ] 填入实际 AdSense ID 到 `templates/shared/config.json` (流量达标后)
- [ ] 在 Google Search Console 验证站点所有权 (加速索引)
- [ ] 发布每日推广文案到对应社区
- [ ] (可选) 首次痛点需要图表时, 按附录 A + B 升级站点为 React 项目

---

## 附录 A: bklit-ui 图表使用规范

### A.1 何时启用 (needs_chart 判断)

| 触发场景 | 示例 | 是否启用 |
|---------|------|----------|
| 展示时间趋势 | 价格走势、增长曲线 | ✅ Line / Area / Live Line |
| 展示占比分布 | 支出分类、市场份额 | ✅ Pie / Ring / Funnel |
| 多维数据对比 | 雷达图、性能对比 | ✅ Radar / Bar |
| 地理区域数据 | 各国 GDP、区域热度 | ✅ Choropleth |
| 流量流程分析 | 漏斗转化、能源流向 | ✅ Funnel / Sankey |
| 进度指标仪表 | 完成率、KPI | ✅ Gauge / Ring |
| 纯交互工具 (无图表) | 计算器、估算器 | ❌ 走纯 HTML 模板 |
| 纯信息汇总 (无图表) | 推荐清单 | ❌ 走纯 HTML 模板 |

决策原则: **能不升级就不升级。纯 HTML 能表达的绝不引入 React。**

### A.2 bklit-ui 简介

- 仓库: https://github.com/bklit/bklit-ui
- 文档站: https://ui.bklit.com
- 技术栈: React + Visx + Motion + Tailwind 4
- 分发方式: shadcn registry (`ui.bklit.com/r/*.json`)

### A.3 可用图表类型

| 图表 | registry URL |
|------|--------------|
| area-chart | `ui.bklit.com/r/area-chart.json` |
| bar-chart | `ui.bklit.com/r/bar-chart.json` |
| line-chart | `ui.bklit.com/r/line-chart.json` |
| live-line-chart | `ui.bklit.com/r/live-line-chart.json` |
| pie-chart | `ui.bklit.com/r/pie-chart.json` |
| ring-chart | `ui.bklit.com/r/ring-chart.json` |
| radar-chart | `ui.bklit.com/r/radar-chart.json` |
| gauge-chart | `ui.bklit.com/r/gauge-chart.json` |
| funnel-chart | `ui.bklit.com/r/funnel-chart.json` |
| sankey-chart | `ui.bklit.com/r/sankey-chart.json` |
| candlestick-chart | `ui.bklit.com/r/candlestick-chart.json` |
| choropleth-chart | `ui.bklit.com/r/choropleth-chart.json` |
| composed-chart | `ui.bklit.com/r/composed-chart.json` |

### A.4 升级步骤

1. 项目脚手架 (详见附录 B 第 B.4 节)
2. 拉取图表组件: `npx shadcn@latest add https://ui.bklit.com/r/{chart-name}.json`
3. 配套 UI 组件从 HeroUI 拉取 (附录 B)
4. 推送 `.github/workflows/deploy.yml`
5. 开启 Pages (source: GitHub Actions): `PUT /repos/{owner}/{repo}/pages` body `{"build_type":"workflow"}`
6. shared 基建放入 `public/shared/`, 在 index.html 引用

### A.5 注意事项

- 构建慢: React 项目首次 Actions 构建约 2-3 分钟
- Bundle 体积: 仅拉取用到的图表组件
- 数据准备: 至少 10 条结构化示例数据
- SSR: 用 Vite 纯 SPA, 无需 Next.js
- 主题: bklit-ui 与 HeroUI 都基于 Tailwind 4, 主题协调

---

## 附录 B: HeroUI UI 组件规范

### B.1 选用优先级 (必须严格遵守)

1. **图表组件**: 必须用 bklit-ui (附录 A)
2. **其他 UI 组件**: 优先使用 HeroUI (`@heroui/react`). HeroUI 满足需求时禁止引入其他 UI 库.
3. **HeroUI 缺失的组件**: 才允许引入其他成熟开源库, 优先级:
   - shadcn/ui (`ui.shadcn.com`, CLI 拉取源码)
   - Radix UI primitives (`@radix-ui/*`)
   - Headless UI (`@headlessui/react`)
   引入时必须在 README.md 注明 "HeroUI 未提供 X, 改用 Y".
4. **禁止**: 手写 UI 组件; 引入 MUI / Chakra / Ant Design / Mantine 等其他 UI 库.

### B.2 HeroUI 简介

- 仓库: https://github.com/heroui-inc/heroui
- 文档站: https://heroui.com
- Storybook: https://storybook-v3.heroui.com/
- 前身: NextUI (v3 起改名 HeroUI)
- 技术栈: React 19 + React Aria + Tailwind CSS v4
- 分发: npm 包 `@heroui/react` 或子包 `@heroui/button` 等
- 许可: MIT

### B.3 关键特性

- Accessible by default (React Aria, WCAG 合规)
- Tailwind v4 原生 (无 CSS-in-JS 运行时)
- Compound components (`Card.Header`, `Select.Item`)
- 零 Provider 包装
- AI 原生 (MCP server + llms.txt)
- 生产级稳定

### B.4 React 项目集成步骤

#### B.4.1 项目脚手架

```
site-YYYYMMDD-{slug}/
├── src/
│   ├── App.tsx                  # 主组件 (HeroUI 布局 + bklit-ui 图表)
│   ├── main.tsx                 # 入口
│   ├── components/ui/{chart}.tsx # bklit-ui 拉取的图表组件
│   ├── styles/globals.css        # Tailwind v4 + @heroui/react/styles
│   └── data/sample-data.ts      # 至少 10 条结构化数据
├── public/shared/               # 复用 hub 的 shared 基建
├── package.json
├── vite.config.ts
├── tsconfig.json
├── tailwind.config.ts
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
  }
}
```

仅在 HeroUI 缺失某组件时才追加 `@radix-ui/*` 或 `@headlessui/react`, 并在 README.md 注明原因.
严禁出现 `@mui/*`, `@chakra-ui/*`, `@mantine/*`, `antd`.

#### B.4.3 全局样式 `src/styles/globals.css`

```css
@import "tailwindcss";
@import "@heroui/react/styles";

:root {
  --color-primary: #2563eb;
  --color-bg: #f7f8fa;
}
```

#### B.4.4 主组件示例 `src/App.tsx`

```tsx
import {Button, Card, CardHeader, CardBody, Input, Select, SelectItem}
  from '@heroui/react';
import {BarChart} from './components/ui/bar-chart';
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

### B.5 组件选用清单 (参考)

| 场景 | 推荐 HeroUI 组件 | 配套 bklit-ui 图表 |
|------|------------------|-------------------|
| 数据仪表盘 | Card / Table / Tabs / Chip | Line / Area / Bar |
| 财务计算器 | Input / Select / Button / Modal | Pie / Ring |
| 资讯站升级 | Card / Chip / Tabs / Skeleton | Bar / Funnel |
| 进度追踪 | Progress / CircularProgress / Badge | Gauge / Ring |
| 多维对比 | Table / Modal / Accordion | Radar / Composed |

完整组件列表: https://heroui.com/docs/components

### B.6 主题约束

- 禁止绕过 HeroUI 自行手写组件样式, 通过 theme / class variants 扩展
- 暗色模式沿用 HeroUI 的 `dark` class 机制, 与 hub `base.css` 协调
- CSS 变量保留在 `src/styles/globals.css`, HeroUI 与 bklit-ui 共享
- 响应式用 Tailwind v4 的 `sm:` / `md:` / `lg:` 断点

---

**最后更新**: 2026-07-03
**文档版本**: v1.3
**维护者**: 自动化任务 + 用户手动跟进