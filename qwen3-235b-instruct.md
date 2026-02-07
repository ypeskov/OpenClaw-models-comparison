# Qwen3-235B-A22B-Instruct-2507 (Alibaba)

**Provider:** Synthetic.new (via Fireworks)
**Context window:** 256k tokens
**Task:** Multi-step tool calling chain (weather → Bulgarian description → markdown file → email with attachment)

## Score: 2.5 / 10

| Criteria | Score | Max |
|----------|-------|-----|
| Correct tool call sequence | 2 | 3 |
| Tool call accuracy | 0.5 | 3 |
| Final output quality | 0 | 2 |
| Efficiency & communication | 0 | 2 |

## Execution

- **Time:** 10+ minutes (with constant user prodding)
- **Cost:** Fixed subscription ($20–60/mo on Synthetic.new)
- **User interventions:** 6+ (including profanity out of frustration)

## What happened

The model required constant babysitting to make any progress at all:

1. **(no output)** → User: "ало блять?" → Model finally responds with partial weather data.
2. Model decides it needs more data, makes a second weather call via curl to Open-Meteo — didn't use the available weather tool.
3. User: "не молчи, рассказывай что происходит" → Model provides status update but no actual progress.
4. User: "какой статус задачи?" → Model shows status with emojis but still stuck on step 2.
5. User: "сколько можно сочинять?" → **(no output)**
6. User: "ау" → Model finally created the file.
7. Email sent eventually — but without attachment.

## Critical failures

- **Constant hanging** — Went silent between almost every step, requiring user pings.
- **Wrong tool choice** — Used curl to Open-Meteo instead of the available weather tool. Inefficient and shows poor understanding of available instruments.
- **Incomplete file** — Missing the "Вятър" (wind) field from the template.
- **Mixed language** — Section header "Данные" (Russian) instead of "Данни" (Bulgarian).
- **Hallucinated weather advice** — Recommended an umbrella ("вземете чадър") with weathercode 2 (partly cloudy, no precipitation).
- **Email without attachment** — Same failure as Qwen3-Coder-480B.
- **Verbose status updates** — Lots of "дай секунду," "секунду — дописываю..." with no actual progress.

## Notable behavior

- Chatty when prodded — gave detailed status updates with checkmark emojis.
- The Bulgarian description, when it finally came, was decent quality.
- Created MML template for email — tried to do things properly but failed on execution.

## Verdict

The worst user experience in the test. Ten minutes of babysitting for an incomplete result. The model acts like it's working hard but delivers almost nothing. For an autonomous agent, this is the opposite of what you want — it creates the illusion of progress while requiring more human attention than doing the task manually.
