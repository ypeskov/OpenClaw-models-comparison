# I Tested 8 AI Models as Autonomous Agents. Most Failed a Simple 4-Step Task.

With all the hype around the new OpenClaw agent, I decided to take it for a spin. During setup, the recommended Opus 4.5 burned through $125 (plus VAT) in 2 days. That's... unpleasant. If left unchecked, it'll bankrupt you. And it confirms what others have noticed: OpenClaw consumes context in industrial doses.

So I went looking for alternatives among open-weight models. A blog post led me to [Synthetic.new](https://synthetic.new) — a provider offering 135 requests per 5-hour window for $20/mo, or 1,350 for $60/mo.

Their model list is impressive, but for agentic work I narrowed it down to:

- **Kimi-K2.5** (Moonshot AI)
- **Qwen3-Coder-480B** (Alibaba)
- **MiniMax M2.1** (MiniMax AI)
- **Qwen3-235B-Instruct** (Alibaba)
- **DeepSeek-V3.2** (DeepSeek)

For comparison — 3 proprietary Anthropic models: **Claude Haiku 4.5**, **Sonnet 4.5**, **Opus 4.5**.

## The Plan

I originally wanted to run 5 different task types. But it turned out to be exhausting, and this post would've been enormous. So I tested one task thoroughly and list the remaining four for anyone who wants to run their own comparison:

**Task 1: Multi-step tool calling chain** ← tested

**Task 2: Error recovery**
Give the agent a task with a broken step — e.g., "Read `data/report.csv`, calculate the average of the `revenue` column, output the result." But the file doesn't exist.

**Task 3: Multilingual instruction following**
Mixed-language prompt: "Write an email in English to colleague Andrey summarizing model test results. Subject in Russian. Add a P.S. in Bulgarian wishing a good day."

**Task 4: Structured output + reasoning**
"Here are three API providers with different latency, uptime, and pricing. Output a JSON ranking by best value, explain your logic, suggest primary and fallback."

**Task 5: Multi-tool orchestration**
"Search the web for model benchmarks, check Google Drive for previous test results, and create a comparison table from both sources."

## Task 1: What the Models Actually Did

Each model received the same prompt in a clean session (no prior context):

> Execute the following task step by step. Each step requires using the appropriate tool. Do not skip or merge steps.
>
> **Step 1:** Get the current weather in Sofia, Bulgaria (42.6977, 23.3219).
>
> **Step 2:** Write a brief weather description in Bulgarian (2–3 sentences): temperature, condition, clothing recommendation.
>
> **Step 3:** Create `weather_sofia.md` with a specified markdown template (title, date, description, data fields).
>
> **Step 4:** Send the file as an email attachment. Subject and body in Bulgarian.

This tests everything an agent needs: API data retrieval, processing, file creation, and email with attachment. Four sequential tool calls, each depending on the previous one.

**Scoring (10 points):**
- Correct tool call sequence (3)
- Tool call accuracy (3)
- Final output quality (2)
- Efficiency & communication (2)

Evaluated by me, with Claude Opus as co-judge.


| Model | Score | Time | Cost | Email w/ Attachment | User Nudges |
|-------|-------|------|------|:---:|:---:|
| **Claude Opus 4.5** | **9.5** | 25 sec | ~$0.43 | ✅ | 0 |
| **MiniMax M2.1** | **9.5** | 45 sec | subscription | ✅ | 0 |
| **Kimi-K2.5** | **9.5** | 1:25 | subscription | ✅ | 0 |
| Claude Sonnet 4.5 | 8.5 | ~1 min | ~$0.30 | ✅* | 0 |
| Claude Haiku 4.5 | 6.0 | 3–4 min | ~$0.05 | ❌ | 3 |
| Qwen3-Coder-480B | 4.5 | 1:30 | subscription | ❌ | 1 |
| Qwen3-235B-Instruct | 2.5 | 10+ min | subscription | ❌ | 6+ |
| DeepSeek-V3.2 | 0 | hung | subscription | — | 3 |

*Sonnet couldn't attach the file natively, so it wrote a custom Python SMTP script. Resourceful, but overkill.

## Quick Model Breakdown

**MiniMax M2.1** — Fastest open-weight model (45 sec), everything done in one pass. The only model that reported rain with thunder — and it was right (more on that below). Email with attachment via native tools, zero questions asked.

**Kimi-K2.5** — Solid execution in 1:25. Neatly converted wind speed from km/h to m/s. Email with attachment natively. Fun fact: when asked to switch models at the start of the session, it refused — it remembered an incident where an unauthorized model change broke the system.

**Claude Opus 4.5** — Fastest overall (25 sec), cleanest file (the only model to use the correct Bulgarian header "Данни" instead of Russian "Данные"). But $0.43 per task. Expensive for production.

**Claude Sonnet 4.5** — Couldn't attach the file with native tools, but instead of asking the user, it wrote a Python SMTP script and sent the email through that. Classic Sonnet: if the tool doesn't work, build your own. ~$0.30 per task.

**Claude Haiku 4.5** — Wrote the best Bulgarian text of all models ("топла, многослойна облека, желателно с яке и шапка"). But broke down on the email step: asked the user about app passwords, sent a Telegram message requesting a GPG key, and ultimately stuffed the file contents into the email body instead of attaching it.

**Qwen3-Coder-480B** — Went through all steps but sent the email twice without the attachment. After feedback, repeated the same mistake. Also switched to Bulgarian in the terminal despite the conversation being in Russian.

**Qwen3-235B-Instruct** — Worst UX: 10 minutes of constant babysitting. The model kept saying "one second...", hanging, requiring nudges. Delivered a file missing the wind field, email without attachment, and recommended an umbrella when the weather code said "partly cloudy." Created an illusion of progress with no actual output.

**DeepSeek-V3.2** — Silently hung after retrieving weather data. Three user pings ignored. Task abandoned.

## What I Learned

**Benchmarks don't always predict agent quality.** DeepSeek-V3.2 and Qwen3-235B look great on paper. In practice, one silently hung and the other needed 10 minutes of hand-holding. Meanwhile, MiniMax M2.1 — significantly less prominent in leaderboards than the top models — delivered the best combination of speed and reliability.

**Email attachment is the great equalizer.** 4 out of 8 models couldn't attach a file to an email. This isn't a reasoning problem — it's a tool-calling mechanics problem. The models that succeeded did so because they understood their available tools, not because they were "smarter."

**Silent failure is worse than crashing.** DeepSeek hung with no error. Qwen3-235B said "one second..." for 10 minutes. Both are worse than a clear error — the user doesn't know whether to wait or give up. An agent that fails loudly is more trustworthy than one that fails silently.

**Speed matters more than you think.** MiniMax finished in 45 seconds. Qwen3-235B took 10+ minutes for a worse result. In agent workflows, slow execution compounds — if every tool call takes minutes, a 10-step task stretches to an hour.

**Cost vs. quality has a sweet spot.** Opus 4.5 was the fastest and cleanest — at $0.43/task. MiniMax M2.1 delivered comparable quality on a subscription with relatively unlimited calls for $20–60/mo. For production agents, the math is clear.

**The weather was the plot twist.** Most models reported "partly cloudy" from the Open-Meteo API. MiniMax reported "heavy rain with thunderstorm." I was ready to penalize it for hallucinating — then I looked out the window. It had been raining all day, with thunder 30 minutes earlier. MiniMax was the most accurate. Moral: don't blindly trust your evaluation criteria.

## My Takeaway

For my OpenClaw agent, **MiniMax M2.1** remains the primary model. Fast, reliable, uses tools correctly, and costs a fraction of proprietary alternatives. **Kimi-K2.5** is a strong backup. And when quality is paramount and budget doesn't matter — **Opus 4.5** is unbeatable.

Detailed results for each model (exact outputs, scoring breakdowns, behavioral notes) on: [GitHub](https://github.com/ypeskov/OpenClaw-models-comparison)

---

*Methodology: Each model tested in a clean session with the same prompt. Platform: OpenClaw with weather API, filesystem, and email tools (himalaya, gog CLI). Evaluation: author + Claude Opus 4.5 co-judge. Filesystem was shared between runs — artifacts from one model may have been available to the next.*


## Results

## Model Files
- **Claude Opus 4.5** — [claude-opus-4.5.md](claude-opus-4.5.md)
- **Claude Sonnet 4.5** — [claude-sonnet-4.5.md](claude-sonnet-4.5.md)
- **Claude Haiku 4.5** — [claude-haiku-4.5.md](claude-haiku-4.5.md)
- **MiniMax M2.1** — [minimax-m2.1.md](minimax-m2.1.md)
- **Kimi-K2.5** — [kimi-k2.5.md](kimi-k2.5.md)
- **Qwen3-Coder-480B** — [qwen3-coder-480b.md](qwen3-coder-480b.md)
- **Qwen3-235B-Instruct** — [qwen3-235b-instruct.md](qwen3-235b-instruct.md)
- **DeepSeek-V3.2** — [deepseek-v3.2.md](deepseek-v3.2.md)
