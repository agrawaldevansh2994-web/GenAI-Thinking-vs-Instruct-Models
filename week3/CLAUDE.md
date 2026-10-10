# CLAUDE.md — Thinking vs Instruct Models (ExcelR GenAI Project)

## What this project is
30-day ExcelR Solutions project comparing **Thinking models** (show reasoning) vs **Instruct models** (answer directly) on the same tasks. Goal: measure accuracy, speed, answer quality, reasoning quality, cost — then recommend when to use each type (e.g. for a student chatbot).

There is no single "right" result. Deliverable quality = measured data + a reasoned opinion.

## Models (4)
| Model | Type | Provider / access | Notes |
|---|---|---|---|
| GPT-4o mini | Instruct | OpenAI API | `test_openai_model` |
| o3-mini | Thinking | OpenAI API (Responses API) | `test_o3_model` |
| DeepSeek-R1 | Thinking | Hugging Face router (`https://router.huggingface.co/v1`, model `deepseek-ai/DeepSeek-R1:novita`), now on paid HF credits ($5 bought; Novita $0.70/M in, $2.50/M out) | No free DeepSeek route exists (HF free tier depleted, Groq dropped it, OpenRouter `:free` pulled). Needs `HF_TOKEN` (Colab secret). Notebook renamed `Genai_Week3.ipynb` |
| GPT-OSS-20B | Instruct | Groq (`https://api.groq.com/openai/v1`) | **Replaces Llama-3.1 8B from the BRD** — be ready to justify this substitution |

Every model-call function returns `{"question", "response", "response_time"}`.

## Tasks
| Task | Dataset | BRD plan | Grading |
|---|---|---|---|
| Math | GSM8K (`load_dataset("gsm8k","main",split="test")`, shuffle seed=42) | 50 questions (plan table) | Auto: last number in response vs `#### answer` |
| Coding | HumanEval | 25 problems (plan table; BRD text says 50 — confirm which) | Run code against tests, manual check |
| Q&A | TriviaQA / own set | 50 questions | Manual 1–5 quality + correct Y/N |

## Scorecard targets (from BRD)
Accuracy ≥ 70% · Avg quality ≥ 3.5/5 · Instruct faster than Thinking · Reasoning trace logical (Thinking only) · Cost per query documented (no target).

## Status
| Phase | Days | State |
|---|---|---|
| Setup, Learn the models, Build script | 1–12 | Done |
| Week 2: 10 common questions × 4 models, scored | — | Done (`Thinking_vs_Instruct_Models_Week2_fixed2.ipynb`) |
| Week 3 (teammate's notebook) | ~13+ | **Built by a teammate, NOT yet reviewed — audit first** |
| GSM8K 50-question run | 13–16 | Code ready in Week 2 notebook; needs DeepSeek key, then a clean run |
| HumanEval / Q&A runs | 17–23 | Not started |
| Analysis, charts, report (2–4 pp), 10-slide deck | 24–30 | Not started |

## FIRST TASK: audit the Week 3 notebook
Treat it as unreviewed. Check:
1. **Question integrity** — exactly N *unique* questions per model (`df.groupby("Model")["question"].nunique()`); no repeated question sets. (Earlier a teammate reran the same 10 questions instead of 50 GSM8K.)
2. **Reproducibility** — dataset sampling seeded; sample size matches the plan.
3. **Question No assigned by position** (`groupby("Model").cumcount()+1`), never by text-matching a dict (text drift silently dropped GPT-4o mini rows from grading before).
4. **Error handling** — retries only on transient errors (429/5xx/timeouts); 401/402/403/404 fail fast and mark the model disabled. Failed calls must never be silently graded as answers.
5. **Grader validity** — auto-grader takes the last number in the response; thinking models can mention numbers after the answer. Spot-check ~10 rows by hand.
6. **Code execution (HumanEval)** — model-generated code must run in a subprocess with a timeout, never `exec()` in the notebook process.
7. **Model/provider consistency** — same 4 models, same prompts, no per-model prompt tweaks unless documented.
8. **Secrets** — no API keys hard-coded or committed; outputs cleared of anything sensitive.
9. **Cached CSVs vs current code** — stale CSVs can carry old question wording; regenerate or verify before merging.
Report findings as a table: issue · where · impact on results · fix.

## Notebook structure (Week 2 notebook)
Setup + per-model test functions → 10-question runs → per-model CSV caches → merge into `combined_df` (Question No by position) → Question Type / Expected Answer mapping → auto + manual grading → scorecards (per model, per type) → xlsx export → Day 13–16 GSM8K section (retry loop with fail-fast on non-retryable status codes, `disabled_models` tracking).

## Conventions
- Secrets: Colab `userdata` in Colab; **`.env` + `python-dotenv` locally** (`OPENAI_API_KEY`, `DEEPSEEK_API_KEY`, `GROQ_API_KEY`). Add `.env` to `.gitignore`. Never print keys.
- Keep per-run CSV caches; never overwrite results without a dated copy.
- Log per call: model, question no, response, response_time, tokens (prompt/completion) so cost/query can be computed from provider pricing.
- Retry: transient errors only, exponential backoff, max 3 attempts.
- Model type labels: Thinking = DeepSeek-R1, o3-mini · Instruct = GPT-4o mini, GPT-OSS-20B.
- Prefer small diffs to existing notebooks; explain what changed and any gotchas.

## Deliverables (final submission)
1. Excel scorecard (accuracy, response time, quality per model per task)
2. Colab/Jupyter notebook with the API script
3. 2–4 page report: most accurate? fastest? best for a student chatbot?
4. 10-slide deck with ≥ 2 comparison charts
5. (Bonus) interesting reasoning traces from DeepSeek-R1 / o3-mini

Existing assets: `model_comparison.py` (standalone 4-model CSV logger), `Model_Comparison_Scorecard.xlsx` (formula-driven Summary tab), `Day1_Progress_Report.docx`.

## Open questions to resolve with the user
- HumanEval size: 25 (plan table) or 50 (dataset section)?
- Is the DeepSeek native API key created and stored?
- What does the Week 3 notebook cover (scope/days)?
