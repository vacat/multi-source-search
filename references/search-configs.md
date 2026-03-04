# 搜索配置参考

## 技术领域搜索配置

### AI/大模型领域

```yaml
topic: "大模型/LLM"
queries:
  - "LLM large language model latest research arXiv 2026"
  - "GPT-4.5 GPT-5 Claude 4 Gemini 2 latest release"
  - "大模型 最新进展 2026"
  - "推理模型 reasoning model o1 o3 deepseek"
  - "LLM inference scaling test-time compute"
keywords:
  - GPT, Claude, Gemini, Llama, DeepSeek
  - reasoning, inference, scaling
  - MoE, multimodal, agent
```

### Agent 框架领域

```yaml
topic: "Agent框架"
queries:
  - "AI Agent framework 2026 LangGraph AutoGen CrewAI"
  - "MCP Model Context Protocol latest"
  - "Agent 框架 应用 最新进展"
  - "multi-agent system orchestration"
  - "Autonomous AI agent production"
keywords:
  - MCP, A2A, agent protocol
  - LangGraph, AutoGen, CrewAI, Dify
  - tool use, function calling
```

### 机器人/具身智能领域

```yaml
topic: "机器人/具身智能"
queries:
  - "embodied AI robotics humanoid robot 2026"
  - "Tesla Optimus Figure AI Boston Dynamics latest"
  - "人形机器人 具身智能 最新进展"
  - "robot learning manipulation VLA model"
  - "sim-to-real robot policy"
keywords:
  - humanoid, embodied AI, VLA
  - manipulation, locomotion
  - sim-to-real, teleoperation
```

### 生成式推荐/搜推广领域

```yaml
topic: "生成式搜推广"
queries:
  - "generative recommendation GenRec 2026"
  - "OneRec HSTU GR4AD large scale recommendation"
  - "生成式推荐 搜推广 最新"
  - "generative search advertising ranking"
  - "LLM for recommendation system"
keywords:
  - GenRec, generative retrieval
  - HSTU, OneRec, GR4AD
  - sequential recommendation
```

## 搜索参数调优

### 高时效性模式（适合日报）

```yaml
filters:
  freshness: "past_week"
  min_score: 25
  max_results: 5
  deduplicate: true

scoring:
  weights:
    freshness: 0.3
    authority: 0.2
    relevance: 0.3
    completeness: 0.2
```

### 深度研究模式（适合专题）

```yaml
filters:
  freshness: "past_month"
  min_score: 20
  max_results: 10
  deduplicate: true

scoring:
  weights:
    freshness: 0.15
    authority: 0.3
    relevance: 0.35
    completeness: 0.2
```

### 广泛扫描模式（适合趋势）

```yaml
filters:
  freshness: "past_month"
  min_score: 15
  max_results: 20
  deduplicate: true

scoring:
  weights:
    freshness: 0.2
    authority: 0.2
    relevance: 0.3
    completeness: 0.3
```

## 权威来源白名单

### 顶级会议/期刊

- arXiv (cs.AI, cs.CL, cs.LG, cs.RO)
- NeurIPS, ICML, ICLR
- ACL, EMNLP, NAACL
- CVPR, ICCV, ECCV
- RSS, CoRL (机器人)

### 公司技术博客

- OpenAI Blog
- Anthropic Research
- Google AI Blog
- DeepMind Blog
- Meta AI Research
- 阿里达摩院
- 字节跳动技术团队
- 腾讯技术工程

### 技术媒体

- 英文: TechCrunch, The Verge, MIT Technology Review
- 中文: 机器之心, 量子位, AI科技评论

### GitHub 趋势

- Trending repositories
- Awesome lists
- Official organization repos
