---
name: interview-audio-review
description: Use when the user asks to analyze, transcribe, review, or extract questions from an interview recording, mock interview, screening call, or technical/behavioral interview audio or video, including recordings stored in Voice Memos or another named app.
---

# Interview Audio Review

## Source Handling

- Follow explicit source instructions before looking elsewhere. If the user says to open or use Voice Memos/语音备忘录 or another app, use the appropriate app/tool first and locate the recording there.
- If the user names a file or path, process that exact file when accessible. If both an app and filename are specified, open the app, locate that recording, and export/share it only if a local file is needed for transcription.
- If no source is specified, choose the most recent relevant media file in the current working folder. If several plausible recordings remain and choosing could analyze the wrong interview, ask a brief clarification.
- If the user corrects the source or workflow, stop unrelated discovery/transcription work and continue from the corrected source.

## Workflow

1. Locate the recording according to Source Handling and obtain an accessible audio/video file when transcription requires one.
2. Transcribe the full recording to text using the best available local or platform audio transcription capability. Preserve speaker turns when possible.
3. Identify speaker roles from context:
   - Treat short prompting turns, follow-ups, and topic-setting turns as interviewer content.
   - Treat longer explanatory turns, self-introductions, project descriptions, and solution narratives as candidate content.
   - If roles are uncertain, label them as `Speaker A` and `Speaker B` and state the uncertainty.
4. Extract interviewer questions, including follow-up questions. Normalize wording without changing intent.
5. Always create a Markdown interviewer-question list first. This is the primary artifact and must contain only cleaned interviewer questions grouped by topic.
6. Always create a separate review Markdown file as the second artifact. It must include interviewer questions, cleaned candidate answers, and concrete improvement suggestions.
7. Always update or create a cumulative question-statistics Markdown file as the third artifact. It tracks how often normalized interviewer questions appear across processed recordings.
8. Respect any user-specified destination folder, especially Obsidian vault paths. If no destination is specified, create files in the current working folder.
9. Keep outputs organized: create one subfolder per recording/company for the question and review files, and keep the cumulative statistics file in a `统计` subfolder.

## Output Files

For Obsidian vault destinations, use this folder layout:

```text
<destination>/<recording-or-company-name>/<recording-or-company-name>-questions.md
<destination>/<recording-or-company-name>/<recording-or-company-name>-review.md
<destination>/统计/interview-question-stats.md
<destination>/统计/questions/<category>/<question-slug>.md
```

Do not place per-interview artifacts directly in the destination root unless the user explicitly asks.

Create the question file with a clear name:

```text
interview-questions-YYYYMMDD-HHMM.md
```

Use this structure:

```markdown
# Interview Questions

Source: <audio filename>
Recorded: <recording date/time from app or file metadata, if available>
Generated: <local date and time>
Transcript source: <tool or source used, e.g. macOS 语音备忘录听写文本>

## <Topic>

1. <normalized interviewer question>
2. <normalized interviewer question>
```

Group questions by topic whenever the interview has clear sections. Common topics include:

- 开场
- Java 集合与并发
- MySQL
- Redis
- 项目
- 算法
- 反问
- 其他

Do not include candidate answers in the question file unless the user explicitly asks.

Always produce a separate review file. Use a file name such as:

```text
interview-review-YYYYMMDD-HHMM.md
```

The review file may include:

```markdown
# <Company or Source> 面试复盘

Source: <audio filename>
Recorded: <recording date/time from app or file metadata, if available>
Question list: [[<question file>]]

## 总体问题

- <top issue>

## 逐题复盘

### <question>

- 回答整理：<cleaned candidate answer>
- 改进意见：<specific feedback>
- 复习重点：<knowledge to review>
- 更好的回答思路：<concise improved outline>
```

Always update a cumulative statistics file, unless the user explicitly opts out:

```text
统计/interview-question-stats.md
```

For Obsidian vaults, make the statistics Dataview-backed by default:

1. Store each normalized interviewer question as one data note under `统计/questions/<category>/`.
2. Use `统计/interview-question-stats.md` as the Dataview dashboard only; do not manually maintain static category tables there.
3. When processing a new recording, update the matching data note by normalized/semantic question identity: increment `count`, update `latest`, and merge new wording into `similar`.
4. If the Dataview plugin is unavailable or the user explicitly requests static tables, fall back to Markdown tables with the same columns and sort order.

Each question data note must use these frontmatter fields:

```markdown
---
type: interview-question-stat
category: Java
question: "HashMap 和 ConcurrentHashMap 有什么区别？"
count: 3
latest: "2026-04-16 14:20"
similar:
  - "ConcurrentHashMap 和 HashMap 区别"
stat_updated: "2026-05-07 18:10"
tags:
  - 面经/问题统计
---
```

Use this Dataview block for each fixed category in `interview-question-stats.md`. Replace `<stats-data-folder>` with the vault-relative path to `统计/questions`, such as `复盘/面经/统计/questions`.

````markdown
## Java

```dataview
TABLE WITHOUT ID question AS "标准问题", count AS "出现次数", latest AS "最近出现", join(similar, "、") AS "相似问法"
FROM "<stats-data-folder>"
WHERE type = "interview-question-stat" AND category = "Java"
SORT count DESC, latest DESC
```
````

Use this Dataview block for the summary:

````markdown
## 分类汇总

```dataview
TABLE WITHOUT ID category AS "分类", length(rows) AS "问题数"
FROM "<stats-data-folder>"
WHERE type = "interview-question-stat"
GROUP BY category
SORT length(rows) DESC
```
````

Static fallback table format:

```markdown
| 标准问题 | 出现次数 | 最近出现 | 相似问法 |
|---|---:|---|---|
| HashMap 和 ConcurrentHashMap 有什么区别？ | 3 | 2026-04-16 14:20 | ConcurrentHashMap 和 HashMap 区别 |
```

Statistics rules:

- Count normalized interviewer-question occurrences, not candidate answers.
- Every extracted interviewer question must appear in the statistics table. Do not drop questions because they are low frequency, broad, or hard to categorize.
- Semantically identical wording counts toward the same `标准问题`.
- Repeated filler or repeated clarifications in the same recording should not inflate counts unless the follow-up tests a distinct point.
- Use exactly these categories as section headings, each with its own table: `Java`, `MySQL`, `Redis`, `Spring`, `中间件`, `AI`, `业务场景`, `其他`.
- `Java` includes JavaSE, JUC, JVM, collections, concurrency, reflection, language basics, and JVM/runtime questions.
- `Spring` includes Spring Framework, Spring Boot, MyBatis, Spring Cloud, Spring transactions, and related framework questions.
- `中间件` includes RocketMQ, Kafka, message queues, RPC, search engines, gateways, and other infrastructure middleware.
- `AI` includes AI tools, agents, LLM application development, prompt engineering, AI Coding, coding agents, and AI-assisted development questions.
- `业务场景` includes project-specific or scenario-design questions such as "项目中如何实现/解决/保证...", including idempotency, consistency, import/export, OOM handling, and business process design when asked in a project context.
- If a question does not clearly fit one of the above categories, put it under `其他`.
- Use the recording date/time for `最近出现`, not the file generation time. Prefer the app-displayed recording time; otherwise use audio file creation metadata; if unavailable, use the recording date visible in the app.
- Dataview category blocks must sort by `count DESC`, then `latest DESC`; static fallback tables must sort by `出现次数` descending, then `最近出现` descending.
- Preserve existing rows and merge in new questions; do not drop historical occurrences.
- Do not include a `来源` column in the statistics tables.

## Transcription Quality

Preserve enough detail to support evaluation:

- Keep filler words only when they affect delivery feedback.
- Mark uncertain words as `[unclear]` instead of guessing.
- Include timestamps when the transcription tool provides them cheaply.
- Note language switches and technical terms.
- If the recording is too noisy or transcription is incomplete, explain the limitation and still extract reliable questions.

## Question Normalization

Normalize questions by:

- Removing stutters, repeated starts, and casual filler.
- Converting fragments into complete questions.
- Keeping technical terms, constraints, and evaluation criteria.
- Preserving follow-ups as separate questions when they test a distinct point.
- Combining repeated clarifications only when they ask the same thing.

Example:

```text
Raw: 那你讲一下，就是 Redis 这块你怎么用的，然后有没有遇到过缓存击穿？
Clean: 请说明你在项目中如何使用 Redis，以及是否处理过缓存击穿问题。
```

## Answer Evaluation

Read `references/evaluation-rubric.md` before writing detailed feedback.

Evaluate the candidate's answers across:

- Accuracy: whether the answer is technically or factually correct.
- Completeness: whether it directly answers the question and covers important details.
- Structure: whether the answer is easy to follow.
- Evidence: whether examples, metrics, tradeoffs, or project details support the answer.
- Communication: whether the speech is concise, confident, and interview-ready.

Give feedback in Chinese by default when the user writes in Chinese. Be specific: quote or paraphrase the answer area briefly, explain the issue, and provide a stronger answer pattern or replacement phrasing.

## Final Response

Summarize:

- The created interviewer-question Markdown file path.
- The created review Markdown file path.
- The updated question-statistics Markdown file path.
- Whether transcription was complete or had quality limitations.
- The top 3-5 answer improvements.
- Any obvious incorrect answers or risky claims that should be corrected before the next interview.
