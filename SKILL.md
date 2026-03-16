---
name: multi-source-search
description: Multi-source information search and aggregation skill. Use when agent needs to collect information from multiple sources (web search, arXiv, GitHub, tech blogs, news) for research, daily reports, or trend analysis. Supports configurable search strategies, result deduplication, and structured output.
---

# 多源搜索方案 (Multi-Source Search)

## 概述

本技能提供一套通用的多源信息搜索和聚合方案，适用于技术日报、研究分析、趋势追踪等场景。

## 核心特性

- **多源并行搜索**：同时查询多个信息源，提高效率
- **智能去重**：基于 URL 和内容相似度自动去重
- **结构化输出**：统一的搜索结果格式，便于后续处理
- **可配置策略**：支持按主题、时间、优先级配置搜索参数
- **容错机制**：单源失败不影响整体搜索

## 可用搜索工具

| 工具 | 用途 | 适用场景 |
|-----|------|---------|
| `kimi_search` | Kimi 互联网搜索 | 最新新闻、论文、博客、综合信息 |
| `web_search` | Brave Search API | 英文内容、技术文档（需配置 API key）|
| `web_fetch` | 网页内容获取 | 获取具体页面详细内容 |
| `kimi_fetch` | Kimi URL 获取 | 获取中文页面详细内容 |
| **multi-search-engine** | 17个搜索引擎聚合 | **无需 API key**，支持时间过滤 |

## 标准搜索流程

### 步骤 1：定义搜索主题

明确需要搜索的方向和关键词：

```yaml
搜索主题:
  - 大模型/LLM 进展
  - Agent 框架与应用
  - 机器人/具身智能
  - 生成式搜推广/GenRec
```

### 步骤 2：构建搜索查询

为每个主题构建多个搜索查询（中英文）：

```yaml
查询模板:
  - "{主题} latest research 2026"
  - "{主题} arXiv March 2026"
  - "{主题} 最新进展"
  - "{主题} GitHub trending"
```

### 步骤 3：并行搜索执行

**方案 A：使用 multi-search-engine（推荐，无需 API key）**

使用 `web_fetch` 直接访问搜索引擎 URL，支持时间过滤：

```javascript
// 中文内容 - 百度
web_fetch({"url": "https://www.baidu.com/s?wd=AI最新进展2026"})

// 中文内容 - 必应
web_fetch({"url": "https://cn.bing.com/search?q=大模型+最新+2026"})

// 英文内容 - Google + 过去24小时（关键！）
web_fetch({"url": "https://www.google.com/search?q=AI+news&tbs=qdr:d"})

// 英文内容 - Google + 过去一周
web_fetch({"url": "https://www.google.com/search?q=LLM+breakthrough&tbs=qdr:w"})

// DuckDuckGo 隐私搜索
web_fetch({"url": "https://duckduckgo.com/html/?q=MCP+protocol+latest"})
```

**时间过滤参数（关键）：**
- `tbs=qdr:d` - **过去24小时**（日报首选）
- `tbs=qdr:w` - 过去一周
- `tbs=qdr:h` - 过去1小时

**方案 B：使用 kimi_search + web_search**

```javascript
// kimi_search 不支持 freshness
kimi_search(query, limit=20)

// web_search 仅在配置 Brave/Perplexity 时支持 freshness
web_search(query, limit=20, freshness="pd")  // 需要 API key
```

**重要说明：**
- `multi-search-engine` 方案 **无需 API key**，通过 URL 参数实现时间过滤
- `kimi_search` **不支持**任何时间过滤参数
- `web_search` 仅在配置 Brave 或 Perplexity 后端时支持 `freshness`

### 步骤 4：结果聚合与去重

- 收集所有搜索结果
- 基于 URL 去重
- 基于标题相似度去重
- 按时间排序

### 步骤 5：内容筛选与评分（含时效性硬过滤）

**硬约束 - 时效性检查：**
- 技术日报：只保留 **48小时内** 发布的内容
- 博客精选：可放宽到 **3-5天内**
- 超过时效的内容直接丢弃，不参与评分

评分维度（仅对符合时效性的内容）：
- **时效性** (0-10)：发布时间越近越高（24小时内得10分，48小时内得7-9分）
- **权威性** (0-10)：来源可信度
- **相关性** (0-10)：与主题匹配度
- **完整性** (0-10)：内容深度和信息量

筛选标准：
- 必须符合时效性要求（硬门槛）
- 总分 ≥ 25 且相关性 ≥ 6

### 步骤 6：获取详细内容（可选）

对高价值结果使用 `kimi_fetch` 或 `web_fetch` 获取完整内容

## 搜索策略配置

### 技术日报配置示例

```yaml
search_config:
  name: "每日技术日报"
  
  sources:
    - kimi_search
    - web_search
  
  topics:
    - name: "大模型/LLM"
      queries:
        - "LLM large language model latest research arXiv 2026"
        - "大模型 LLM 最新进展 2026"
        - "GPT Claude Gemini 新功能发布"
      limit: 20
      
    - name: "Agent框架"
      queries:
        - "AI Agent framework application 2026"
        - "Agent 框架 应用 最新"
        - "MCP protocol LangGraph AutoGen"
      limit: 20
      
    - name: "机器人/具身智能"
      queries:
        - "embodied AI robotics humanoid robot 2026"
        - "人形机器人 具身智能 最新"
        - "Tesla Optimus Figure AI"
      limit: 20
      
    - name: "生成式搜推广"
      queries:
        - "generative recommendation GenRec search advertising 2026"
        - "生成式推荐 搜推广 最新"
        - "OneRec HSTU GR4AD"
      limit: 20
  
  filters:
    min_score: 25
    deduplicate: true
    max_results_per_topic: 5
    max_age_hours: 48  # 技术日报只收录48小时内内容（需在搜索结果中手动检查日期）
  
  output:
    format: "markdown"
    include_summary: true
    include_links: true
    include_analysis: true
```

## 输出格式

### 标准结果结构

```markdown
## {主题名称}

### 核心动态

1. **{标题}** (评分: {总分}/40)
   - 来源: {网站名} | {日期}
   - 一句话摘要: {摘要}
   - 链接: {URL}
   - 评分详情: 时效性{x}/10 | 权威性{y}/10 | 相关性{z}/10 | 完整性{w}/10

2. ...

### 深度解读

{对同主题多条信息的递进分析和关联}

### 机会点分析

- **短期**: {近期可落地的机会}
- **中期**: {3-6个月的发展趋势}
- **长期**: {1年以上的战略方向}
- **风险提示**: {潜在风险和挑战}
```

## 使用示例

### 示例 1：技术日报搜索

```
用户: 准备今天的技术日报

Agent 执行:
1. 加载 multi-source-search skill
2. 使用技术日报配置
3. 并行搜索 4 个主题 × 3 个查询 = 12 次搜索
4. 聚合结果，去重，筛选
5. 生成结构化日报
6. 发布到博客
```

### 示例 2：专题研究搜索

```
用户: 研究 MCP 协议的最新进展

Agent 执行:
1. 定义专题: "MCP Model Context Protocol"
2. 构建查询:
   - "MCP protocol latest updates 2026"
   - "Model Context Protocol Anthropic"
   - "MCP Agent integration examples"
3. 执行搜索，获取前 30 条结果
4. 筛选高评分内容
5. 获取详细内容（fetch）
6. 输出研究报告
```

## 最佳实践

1. **时效性手动过滤**：`kimi_search` 不支持 `freshness` 参数，获取结果后必须手动检查每条内容的日期，过滤过时内容
2. **扩大搜索量级**：建议每主题 limit=20-30，确保返回池子足够大，筛选后保留高质量内容
3. **查询多样化**：同一主题用不同角度查询，提高覆盖率
4. **中英文结合**：技术内容英文更全面，中文有独特视角
5. **来源交叉验证**：重要信息通过多个来源验证
6. **渐进式深入**：先广泛搜索，再对重点内容深度获取

## 故障处理

| 问题 | 原因 | 解决方案 |
|-----|------|---------|
| 搜索结果为空 | 查询过于具体 | 放宽关键词，减少限定条件 |
| 结果重复率高 | 查询相似 | 调整查询角度，增加多样性 |
| 内容质量低 | 来源混杂 | 增加权威性评分权重 |
| 获取详情失败 | 网站反爬 | 使用摘要信息，标记需手动查看 |

## 扩展建议

- 添加更多垂直领域源（如 Hugging Face、Papers With Code）
- 实现搜索结果缓存，避免重复搜索
- 添加用户反馈循环，优化评分算法
- 支持自定义信息源配置
