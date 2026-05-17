# 自动化脚本目录

本目录包含用于知识库评测的辅助脚本：问题编号规范化、提取问答对、调用 Dify API 召回 chunk。

## 脚本说明

| 文件名 | 功能 | 输入 | 输出 |
|--------|------|------|------|
| `preprocess_questions_simple.py` | 为数字编号（如 `1.`）添加“问题”前缀 | 问题文本文件 | 添加前缀后的问题文本 |
| `preprocess_questions_advanced.py` | 支持数字编号、中文编号（一、二、）、特殊格式（`**4．**`） | 问题文本文件 | 格式化后的问题文本 |
| `extract_qa_pairs.py` | 提取问答对，生成 Excel | 格式化后的问题+答案文本 | Excel 文件（question, ground_truth） |
| `retrieve_chunks.py` | 调用 Dify 知识库检索 API，获取每个问题的召回 chunk | 问题文件（每行一个问题） | 文本文件（问题对应的召回片段） |

## 使用流程

1. **准备原始问题文件**（如 `questions_raw.txt`）。
2. **（可选）运行预处理脚本**统一编号格式：
   ```bash
   python preprocess_questions_simple.py questions_raw.txt questions_fixed.txt
   ```
3. **（可选）提取问答对**：若已有问答对文本，运行 `extract_qa_pairs.py` 生成 Excel 测试集。
4. **调用知识库检索**：
   ```bash
   python retrieve_chunks.py questions_fixed.txt retrieval_results.txt
   ```
5. **评估检索结果**：将 `retrieval_results.txt` 和原文档输入 `prompts/retrieval_node_eval.txt` 获得评分。

## 依赖

- Python 3.8+
- `requests`（调用 Dify API）
- `pandas`（仅 `extract_qa_pairs.py` 需要）
- 标准库：`re`, `json`

## 配置与安全

在 `retrieve_chunks.py` 中需配置 Dify API 信息：

```python
RETRIEVE_URL = "https://api.dify.ai/v1/datasets/{dataset_id}/retrieve"
API_KEY = "your-api-key"
```

## 注意事项

- `retrieve_chunks.py` 中的检索参数（Top K、阈值、Rerank 模型）应与你的知识库实际配置一致。
- 原始脚本中的正则表达式可能需要根据文档格式微调。
- 运行脚本前请确认网络可访问 Dify API 端点。
