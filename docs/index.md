# Yuliton API 文档

<div class="hero" markdown>

**统一的 AI API 平台** — 一个接口访问所有主流 AI 模型

[快速开始](#_2){ .md-button .md-button--primary }
[API 参考](yuliton-api.md){ .md-button }

</div>

## 为什么选择 Yuliton？

Yuliton 是一个统一的 AI API 平台，让你通过一个接口访问 OpenAI、Anthropic、腾讯、火山、阿里等主流 AI 模型供应商。

### 核心优势

**统一接口**
:   使用 OpenAI API 格式调用所有模型，无需学习多个 SDK

**零代码迁移**
:   现有 OpenAI/Anthropic 代码只需修改 `base_url` 即可接入

**自动格式转换**
:   Yuliton 自动处理不同供应商之间的请求结构差异

**提供商灵活性**
:   根据需求选择最适合的模型供应商，统一管理配额和计费

## 快速开始

### 1. 获取 API Key

登录 [Yuliton 控制台](https://www.yuliton.com/dashboard)，在「API 密钥」页面创建新的 API Key。

**重要提醒：**
- API Key 创建后请及时保存，关闭页面后将无法再次查看
- 建议为不同项目创建不同的 API Key
- 请勿将 API Key 硬编码在客户端代码中

### 2. 安装 SDK

```bash
# Python
pip install openai

# Node.js
npm install openai
```

### 3. 发出第一个请求

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-Yuliton-api-key",
    base_url="https://api.yuliton.com/v1"
)

response = client.chat.completions.create(
    model="yuliton-llm-3.5",
    messages=[
        {"role": "system", "content": "你是一个专业助手。"},
        {"role": "user", "content": "请用一句话介绍 Yuliton。"}
    ]
)

print(response.choices[0].message.content)
# 预期输出: "Yuliton 是一个统一的 AI API 平台，支持多种模型供应商。"
```

## 支持的模型

| 供应商 | 模型 | 类型 |
|--------|------|------|
| OpenAI | GPT-4o, GPT-4, GPT-3.5-turbo | 文本生成 |
| Anthropic | Claude 3.5 Sonnet, Claude 3 Opus | 文本生成 |
| 腾讯 | 混元系列 | 文本生成 |
| 火山 | 豆包系列 | 文本生成、图像生成 |
| 阿里 | 通义千问系列 | 文本生成、图像生成 |
| Jina | Jina Embeddings, Reranker | 嵌入、重排序 |

## 接入端点

| 端点 | 地址 | 说明 |
|------|------|------|
| 主 API 端点 | `https://api.yuliton.com/v1` | OpenAI 兼容格式 |
| Anthropic 兼容端点 | `https://api.yuliton.com/anthropic/v1` | Anthropic 原生格式 |
| Jina 兼容端点 | `https://api.yuliton.com/jina/v1` | Jina 原生格式 |

## 下一步

- [快速入门](yuliton-api.md) — 5 分钟内开始使用
- [API 参考](yuliton-api.md) — 完整的 API 文档
- [最佳实践](yuliton-api.md) — 生产环境部署指南
- [错误处理](yuliton-api.md) — 错误码和解决方案

---

**文档版本**: 2026-04-28  
**数据来源**: [Yuliton 官方文档](https://www.yuliton.com/docs/zh)  
**维护者**: Yuliton 技术团队
