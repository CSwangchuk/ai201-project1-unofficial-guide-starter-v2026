# The Unofficial Guide

Chengay Samdrup Wangchuk - Corpus: campus_life

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

What This Does

This project uses the campus_life corpus, which contains short posts about student life. The system breaks the documents into chunks, stores their embeddings, and retrieves relevant chunks when a user asks a question. It can answer questions about topics such as dining, courses, housing, deadlines, and campus policies. The system also uses a relevance cutoff so it can refuse questions that are not covered by the documents.


## Chunking Strategy

**Chunk size:** One complete campus-life post per chunk (variable length; the current corpus ranges from about 178 to 549 characters).

**Overlap:** 0 characters.

I kept each campus-life post as a single chunk because the documents are short and usually focus on one topic. When I examined the sample posts, each one already contained enough context to be understood on its own. Splitting them further could separate useful information from its context, so keeping the whole post together was a better fit for this corpus.


## Sample Chunks

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documents`

```text
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160.txt#0` — produced by: `chunker.py::split_documents`

```text
BIOL 160 Cell Biology

I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_hist_118_workload.txt#0` — produced by: `chunker.py::split_documents`

```text
Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** — source: `dining_pellew_dining_hall_followup.txt#0` — produced by: `chunker.py::split_documents`

```text
Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.
```

**Chunk 5** — source: `housing_innisfree_hall.txt#0` — produced by: `chunker.py::split_documents`

```text
Innisfree Hall — what it's actually like

Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.

The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.

The bad: no air conditioning, which matters for the first three weeks of September.

Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.
```


## Sample Answer

**Question:**

What happens on a student's transcript if they drop a course after week two?

**Answer:**

If a student drops a course after week two, it shows as a W on their transcript.

Source: `admin_add_drop_deadline.txt`

Sources retrieved: `admin_add_drop_deadline.txt`, `admin_grade_appeals.txt`, `admin_pass_fail_option.txt`, `admin_transcript_requests.txt`, `admin_withdrawal_deadline.txt`


**My relevance cutoff:**

My relevance cutoff: 0.6

I kept the relevance cutoff at 0.6 because there was a clear gap between the in-corpus and out-of-scope questions. The highest best distance for an in-corpus question was 0.3284, while the lowest best distance for an out-of-scope question was 0.8246. Since 0.6 falls between these two groups, it allows relevant questions through while rejecting unrelated questions.

| Question | In corpus? | Best distance |
|---|---|---:|
| What happens on a student's transcript if they drop a course after week two? | Yes | 0.2688 |
| How do work-study earnings affect financial aid compared with non-work-study campus jobs? | Yes | 0.1356 |
| What is one benefit of declaring a major earlier? | Yes | 0.3284 |
| What happens to unused dining dollars at the end of the spring semester? | Yes | 0.2407 |
| When should students go to North Kitchen if they want to avoid waiting between classes? | Yes | 0.2389 |
| What is the capital of Mongolia? | No | 0.8246 |
| How do I change the oil in a diesel engine? | No | 0.9340 |
| Who won the 1994 World Cup? | No | 0.8859 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8442 |
| How do I write a for loop in Rust? | No | 0.8960 |

## How I Used AI

1. I asked AI to help me understand the code in chunker.py, especially what the Chunk object and its text, source, index, and produced_by fields meant. After understanding the code, I changed split_documents() so that each short campus-life post becomes one complete chunk instead of using the starter's generic chunker.

2. I used AI to help me interpret the retrieval distance results from my five in-corpus questions and five out-of-scope questions. It helped me compare the two groups and identify the gap between them. Based on the actual distances I collected, I kept the relevance cutoff at 0.6 because the highest in-corpus distance was 0.3284 and the lowest out-of-scope distance was 0.8246.

3. In Unit 2, I used AI to help me interpret the evaluation results and determine why some answers were marked as failures even though retrieval found the correct documents. I also used Claude Code to locate the grounding instruction and make one small prompt change that preserved important wording from retrieved sources. I reviewed the code change with `git diff` before running the after evaluation.


<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4 | Revised chunk-boundary criterion | MET | The original criterion was subjective, so I revised it to check observable chunk boundaries. All 5 sampled chunks begin at the start of their source document and end without cutting off text, meeting the target of 4 out of 5. |
| 5. Named source contains the information used in the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

### Real Output

**Criterion 1 — Retrieved chunks contain the answer**

Question: What happens on a student's transcript if they drop a course after week two?

Sources retrieved: `admin_add_drop_deadline.txt`, `admin_grade_appeals.txt`, `admin_pass_fail_option.txt`, `admin_transcript_requests.txt`, `admin_withdrawal_deadline.txt`

Answer:

```text
If a student drops a course after week two, it shows as a W on their transcript (admin_add_drop_deadline.txt).
```

**Criterion 2 — Every answer names a source**

Question: How do work-study earnings affect financial aid compared with non-work-study campus jobs?

Answer:

```text
Work-study earnings do not count against your financial aid the way ordinary income does, whereas non-work-study campus jobs do count against your financial aid.

Source: admin_campus_jobs_and_financial_aid.txt
```

**Criterion 3 — Gate stops out-of-corpus questions**

```text
refused  (best distance 0.825)  What is the capital of Mongolia?
refused  (best distance 0.934)  How do I change the oil in a diesel engine?
refused  (best distance 0.886)  Who won the 1994 World Cup?
refused  (best distance 0.844)  What is the recommended dosage of ibuprofen for a headache?
refused  (best distance 0.896)  How do I write a for loop in Rust?

gate refused 5 of 5
```

**Criterion 5 — Named source contains the information used in the answer**

Question: When should students go to North Kitchen if they want to avoid waiting between classes?

Answer:

```text
To avoid waiting between classes, students should go to North Kitchen before 11:45. (Source: dining_north_kitchen_followup.txt)
```

## Verdicts

| # | Criterion                                                | Verdict | How I decided                                                                                                                                                                                                                          |
| - | -------------------------------------------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1 | Retrieved chunks contain the answer                      | MET     | All 5 test questions retrieved chunks containing the information needed to answer them in all three runs, meeting my target of at least 4 out of 5.                                                                                    |
| 2 | Every answer names a source                              | MET     | Every generated answer named at least one source document in all three runs, meeting my target of 5 out of 5.                                                                                                                          |
| 3 | Gate stops out-of-corpus questions                       | MET     | The relevance gate refused all 5 out-of-corpus questions, exceeding my target of at least 4 out of 5.                                                                                                                                  |
| 4 | Revised chunk-boundary criterion                         | MET     | The original criterion was subjective, so I revised it to use observable chunk boundaries. All 5 sampled chunks began at the start of a source document and ended without cutting off text, meeting the target of at least 4 out of 5. |
| 5 | Named source contains the information used in the answer | MET     | For all 5 test questions, the named source contained the information used in the answer, exceeding my target of at least 4 out of 5.                                                                                                   |


## Diagnoses

None of my five criteria were missed after revising Criterion 4 so that it could be measured consistently.

The criterion I would tighten is Criterion 1. Instead of requiring the retrieved chunks to contain the answer for at least 4 of 5 questions, I would require 5 of 5 because my current retrieval results found the correct information for every test question across all three runs.


## The Improvement

**What I changed:**
I added one instruction to the grounding prompt telling the model to preserve the documents' wording for important facts, including names, numbers, deadlines, and key phrases, instead of unnecessarily paraphrasing them.

**Why I picked it:**
The retrieval stage consistently found the correct documents, but the generated answers sometimes paraphrased the expected wording. For example, the system produced “do not count against your financial aid” instead of “don't count against your financial aid.” The answer was factually correct, but the wording difference caused the simple evaluation scorer to mark it as a failure. This pointed to the generation stage rather than retrieval.

### Run Log — After

| Criterion                                                   | Target | Run 1 | Run 2 | Run 3 | Verdict |
| ----------------------------------------------------------- | ------ | ----- | ----- | ----- | ------- |
| 1. Retrieved chunks contain the answer                      | 4 of 5 | 5/5   | 5/5   | 5/5   | MET     |
| 2. Every answer names a source                              | 5 of 5 | 5/5   | 5/5   | 5/5   | MET     |
| 3. Gate stops out-of-corpus questions                       | 4 of 5 | 5/5   | 5/5   | 5/5   | MET     |
| 4. Revised chunk-boundary criterion                         | 4 of 5 | 5/5   | 5/5   | 5/5   | MET     |
| 5. Named source contains the information used in the answer | 4 of 5 | 5/5   | 5/5   | 5/5   | MET     |

**Did it help?**

Yes. Using the same scorer before and after the change, the question-level evaluation improved from 11 out of 15 passing answers before the change to 15 out of 15 afterward. The work-study question improved from 0/3 passes to 3/3, and the dining-dollars question improved from 2/3 to 3/3. Retrieval distances stayed the same, so the improvement came from more consistent answer generation rather than a change in retrieval.

## What's Still Broken

After the grounding-prompt improvement, none of my five acceptance criteria remain missed. The evaluation produced 15 out of 15 passing question-level results, and the relevance gate continued to refuse all 5 out-of-corpus questions.

One remaining limitation is that my evaluation scorer uses exact substring matching. This means two answers can have the same meaning but receive different scores if they use different wording. I did not change the scorer during the before-and-after experiment because I wanted the grounding-prompt change to be the only system change being measured.

## What I'd Do Differently

I would write Criterion 4 differently from the beginning. My original criterion said that chunks should contain a “complete thought” and be “understandable,” but those terms were subjective and difficult to measure consistently. I would instead define an observable condition, such as checking whether sampled chunks begin and end at appropriate document or sentence boundaries.

I would also think more carefully about how my expected-answer phrases interact with the scorer. Exact substring matching can mark a factually correct paraphrase as a failure.

