# AI Agent Field Report: 8 Models, 1 Task, Real Results

This is a practical **engineering field report**, not a scientific benchmark.  
It documents hands-on testing of 8 AI models as autonomous agents in [OpenClaw](https://github.com/), a terminal-based AI agent platform.

The goal is to give an intuitive, experience-based understanding of how these models behave in real agent workflows.  
Readers looking for rigor can reproduce the setup and run their own comparisons.

## Quick Results

| Model | Score /10 | Time | Cost | Email+Attach |
|-------|-----------|------|------|:---:|
| Claude Opus 4.5 | 9.5 | 25 sec | ~$0.43 | ✅ |
| MiniMax M2.1 | 9.5 | 45 sec | subscription | ✅ |
| Kimi-K2.5 | 9.5 | 1:25 | subscription | ✅ |
| Claude Sonnet 4.5 | 8.5 | ~1 min | ~$0.30 | ✅* |
| Claude Haiku 4.5 | 6.0 | 3–4 min | ~$0.05 | ❌ |
| Qwen3-Coder-480B | 4.5 | 1:30 | subscription | ❌ |
| Qwen3-235B-Instruct | 2.5 | 10+ min | subscription | ❌ |
| DeepSeek-V3.2 | 0 | hung | subscription | — |

*Used a custom Python script instead of native tools.

## Articles

- [English article](article-en.md)
- [Русская статья](article-ru.md)

## Detailed Model Reports

- [Claude Opus 4.5](models/claude-opus-4.5.md)
- [MiniMax M2.1](models/minimax-m2.1.md)
- [Kimi-K2.5](models/kimi-k2.5.md)
- [Claude Sonnet 4.5](models/claude-sonnet-4.5.md)
- [Claude Haiku 4.5](models/claude-haiku-4.5.md)
- [Qwen3-Coder-480B](models/qwen3-coder-480b.md)
- [Qwen3-235B-Instruct](models/qwen3-235b-instruct.md)
- [DeepSeek-V3.2](models/deepseek-v3.2.md)

## Methodology

**Platform:** OpenClaw (terminal AI agent)  
**Model providers:** Anthropic API, Synthetic.new (Fireworks, self-hosted)  

**Task:**  
4-step tool-calling chain — get weather → write Bulgarian description → create markdown file → send email with attachment.

If the final email did **not** include the markdown file as an attachment, the task was considered **failed**, even if intermediate steps succeeded.

**Evaluation:** Human judge + Claude Opus 4.5 co-judge  
**Session isolation:** Each model was tested in a clean conversation session (note: filesystem was shared between runs)

## Scoring Criteria (10 points)

| Criteria | Points |
|----------|--------|
| Correct tool call sequence | 3 |
| Tool call accuracy | 3 |
| Final output quality | 2 |
| Efficiency & communication | 2 |

## Author

Yuriy Peskov — building AI agents and evaluating what actually works in production.

## License

CC BY 4.0 — feel free to share and reference with attribution.
