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
| 4. Sampled chunks contain a complete thought and are understandable on their own | 4 of 5 | ... | ... | ... | ... |
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

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
