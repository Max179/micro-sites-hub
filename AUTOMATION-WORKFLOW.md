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

第 2 步: 选模板 + 填内容
  └─ 判断类型(tool / info / calculator)
     从 hub 仓库 get_file_contents 读取模板
     替换所有 {{PLACEHOLDER}} 占位符

第 3 步: 部署到 GitHub Pages
  ├─ create_repository 创建新仓库 site-YYYYMMDD-{slug}
  ├─ push_files 推送 index.html + shared/* + sitemap.xml + robots.txt
  ├─ 调用 GitHub Pages API 自动开启 Pages (POST /repos/{owner}/{repo}/pages)
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

## 七、原则与边界

### 原则

1. **先跑通流水线,不追求完美** — 每天推进一个项目,不贪多
2. **完全云端执行** — 不依赖本地文件系统,所有数据托管到 GitHub
3. **执行到成功为止** — 遇到错误先排查修复,不中途放弃
4. **幂等性** — 当日已成功则跳过,不重复执行
5. **数据可追溯** — 所有变更通过 git commit 记录,可版本回溯

### 不做的事

- ❌ 自动发帖推广(只生成文案,需人工审核发布)
- ❌ 自动获取 GA 数据(只生成检查清单,需人工登录查看)
- ❌ 自动提交搜索引擎收录(需人工到 Search Console 操作)
- ❌ 编造数据(复盘报告只列指标项,不填假数据)

## 八、当前状态

### 已完成

- [x] 模板系统(3 种类型 + 共享基建)已推送到 `micro-sites-hub`
- [x] 每日生产任务 `767fecac` 已创建并云端化
- [x] 每周复盘任务 `77da2848` 已创建并云端化
- [x] 首次执行已产出 2 个站点:
  - `site-20260703-caffeine-half-life-calculator` ✅ 已上线
  - `site-20260703-reading-time-calculator` ✅ 已上线

### 待办

- [ ] 填入实际 GA ID 到 `templates/shared/config.json`
- [ ] 填入实际 AdSense ID 到 `templates/shared/config.json`(流量达标后)
- [ ] 提交站点到 Google Search Console 收录
- [ ] 发布每日推广文案到对应社区

---

**最后更新**: 2026-07-03  
**文档版本**: v1.0  
**维护者**: 自动化任务 + 用户手动跟进