# Thinking Models vs Instruct Models

**ExcelR GenAI Project, Group 2**

| Team member |
|---|
| Devansh Agrawal |
| Nupur Sharma |
| Karthik Betageri |

## About the project

This project compares **Thinking models** (which reason step by step before answering) with **Instruct models** (which answer directly). All four models were tested on the same tasks and scored on accuracy, response time and answer quality. The aim is to recommend when each type of model is the better choice, for example in a student chatbot.

## Models evaluated

| Model | Type | Access |
|---|---|---|
| DeepSeek-R1 | Thinking | Hugging Face router |
| o3-mini | Thinking | OpenAI API |
| GPT-4o mini | Instruct | OpenAI API |
| GPT-OSS-20B | Instruct | Groq API |

GPT-OSS-20B replaces Llama-3.1 8B Instruct, which was not available on the free/developer tier we used.

## Tasks

| Task | Dataset | Questions | Grading |
|---|---|---|---|
| Math reasoning | GSM8K | 50 | Compared with the ground-truth answer |
| Coding | HumanEval | 25 | Generated code checked for correctness |
| General knowledge Q&A | Selected question set | 50 | Correctness and clarity |

## Final results (accuracy)

| Model | GSM8K | HumanEval | General Knowledge |
|---|---:|---:|---:|
| DeepSeek-R1 | 89.58%* | 80% | 100% |
| o3-mini | 92% | 24% | 100% |
| GPT-4o mini | **94%** | 92% | 100% |
| GPT-OSS-20B | 92% | **96%** | 100% |

\*DeepSeek-R1 had 2 API errors on GSM8K, so its score is 43 correct out of 48 answered questions.

## Key findings

- No single model won every task. GPT-4o mini was best on math, GPT-OSS-20B on coding, and all four tied on general knowledge.
- Thinking models were not automatically more accurate. o3-mini scored lowest on HumanEval (24%).
- Instruct models were much faster. On HumanEval, GPT-OSS-20B averaged about 1.1 s per answer and DeepSeek-R1 about 64 s.
- DeepSeek-R1 gave the most detailed reasoning-style answers. o3-mini gave the most concise ones.
- **Recommendation:** GPT-4o mini for a general student chatbot. GPT-OSS-20B is a strong alternative when coding and speed matter most.

The full analysis and limitations are in the final report notebook.

## Repository structure

| Folder | Contents |
|---|---|
| `Final week/` | Final report notebook (and PDF export), analysis and charts notebook, final presentation |
| `week 1/` | Business requirements document (BRD), participation guidelines, initial notebooks |
| `week2/` | 10-question comparison, GSM8K results, scorecards and charts (earlier runs in `old/`) |
| `week3/` | General Knowledge and HumanEval notebook, raw results and scorecards (CSV) |

## Tools used

Python, Jupyter / Google Colab, pandas, Excel, and the OpenAI, Hugging Face and Groq APIs.
