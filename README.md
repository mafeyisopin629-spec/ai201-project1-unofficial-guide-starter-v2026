# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

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

<!-- Three or four sentences. Which corpus you picked, and the kinds of
     questions your system answers. Write it for someone who has never seen
     this repo.

     Milestone 5. -->

This project uses the `city_guides` corpus, which contains guides about towns, transportation, food, accessibility, and travel around the region. The system lets a user ask specific questions about those guides and retrieves the most relevant chunks before generating an answer. For example, it can answer questions about Kestrelford's restaurants, Brightwater dining hours, railway access, or when to visit Halden Bay. Answers are grounded in the retrieved documents and name the source used.

## Chunking Strategy

**Chunk size:** Up to 800 characters, while keeping whole paragraphs together.

**Overlap:** 0 characters.

The original chunker used fixed 800-character windows and often cut sentences and even words in half. In my first five sampled chunks, I saw endings such as "mino" and "grou" and a chunk beginning with "irts." Because the `city_guides` documents are long guides organized into paragraphs and labeled sections, I changed the chunker to group complete paragraphs until the chunk approaches the 800-character limit. After the change, the corpus went from 51 chunks to 46 chunks, and the new five sampled chunks no longer cut sentences or words in half.

<!-- What about YOUR documents made you pick these numbers? Short posts and
     long sectioned guides don't want the same chunking, and "800 seemed
     reasonable" earns nothing. Point at something you noticed when you read
     the documents in Milestone 1.

     If you changed your mind partway through, say so and say why. That's worth
     more than pretending you got it right first time.

     Milestone 3. -->

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `guide_accessibility.md#0` — produced by: `chunker.py::split_documents`
```text
# Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.

## Straightforward

**Thornby Wells** is the easiest town in the region. It is flat, compact, and
everything is within three minutes of everything else. Parking is free for two
hours anywhere in town and the station is central. The pump room and gardens
are level throughout.

**Marchwood** has a modern tram network with level boarding on all four lines,
running every 8 minutes on weekdays. The city museum and covered market are both
step-free. The distances between districts are the main consideration.
```

**Chunk 2** — source: `guide_corry_vale.md#1` — produced by: `chunker.py::split_documents`
```text
Nothing within the valley is walkable from anything else — the villages are two to four miles apart. There is one taxi, based in the largest village, and it must be booked a day ahead. Most visitors drive between villages and walk the footpaths in between.

## Eat and drink

One pub in the largest village serves food seven days a week. A second, in the third village, opens Thursday to Sunday. There is a farm shop at the valley mouth that sells bread, cheese and little else, and it closes at 4pm. Bring supplies; this is not a place with options.

## What to see
```

**Chunk 3** — source: `guide_givens_mill.md#0` — produced by: `chunker.py::split_documents`
```text
# Givens Mill

Givens Mill is a village of 700 built around a working watermill that still grinds flour commercially. It is the sort of place people visit for an afternoon and then talk about for longer than the visit lasted.

## Getting there

No station and no bus on Sundays; four buses a day from Brightwater on weekdays, taking 30 minutes. Driving is 20 minutes. The village car park holds about forty cars and is full by 11am on summer Saturdays.

## Getting around

Everything is on one street along the river. The mill is at one end and the church at the other, eight minutes apart. The riverside path continues in both directions for as far as you want to walk.

## Eat and drink
```

**Chunk 4** — source: `guide_marchwood.md#0` — produced by: `chunker.py::split_documents`
```text
# Marchwood

Marchwood is the regional hub — 180,000 people, the junction everyone changes trains at, and a city most visitors pass through rather than stop in. That is a mistake, though an understandable one, since almost nothing of interest is near the station.

## Getting there

Every railway line in the region meets here, which is the city's defining feature. Trains to Brightwater run every 40 minutes until 11pm. The airport is 20 minutes out by a dedicated bus that runs every 15 minutes and costs more than the equivalent taxi shared between three people.

## Getting around
```

**Chunk 5** — source: `guide_regional_transport.md#1` — produced by: `chunker.py::split_documents`
```text
The Kestrelford service is hourly on weekdays, two-hourly on Saturdays, and
does not run on Sundays. The Halden Bay coast service runs four times daily
year-round.

## Driving

Roads are good between the towns and poor on the approaches to both Kestrelford
and Halden Bay. The Kestrelford approach is single-track with passing places
for the final eight minutes. The Halden Bay coast road is cut into the cliff
and is slow rather than difficult.

Parking is the constraint rather than driving. Both Halden Bay lots fill by
10am on summer weekends. Kestrelford's lower car park is free and involves a
steep walk up.

## Walking and cycling
```

## Sample Answer

**Question:** How many pubs and cafés are there in Kestrelford?

**Answer:** There are four pubs and two cafés in Kestrelford (`guide_kestrelford.md`).

**Sources retrieved:** `guide_eating.md`, `guide_elder_ness.md`, `guide_kestrelford.md`

**Best distance:** `0.470`

**Relevance cutoff:** `0.60`

For my five in-scope test questions, the best retrieval distances ranged from `0.3657` to `0.4740`.

For my five out-of-scope questions, the best distances ranged from `0.8190` to `1.0180`.

There was a clear gap between the two groups, so I kept the relevance cutoff at `0.60`. This allows the system to answer questions that are well supported by the corpus while refusing questions that are clearly unrelated.

## How I Used AI

**1.** I used AI to help me pressure-test my fourth acceptance criterion after I printed five chunks from the original chunker. I showed the five samples and asked whether my criterion about complete thoughts was measurable. The AI pointed out that all five samples had text cut off at the beginning or end, so I used that evidence to set my target at 4 of 5 readable, self-contained chunks.

**2.** I used AI to help me work through a paragraph-based chunking strategy after the fixed-size chunker was cutting words and sentences in half. It suggested grouping complete paragraphs until the chunk approached the configured size instead of splitting strictly by character position. I added that approach to `split_documents`, reran the index and chunk samples, and verified that the output changed from 51 fallback chunks to 46 paragraph-based chunks with complete sentence boundaries.

     Milestone 5. -->

**1.**

**2.**

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

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->


| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks read as complete thoughts | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Retrieved information is used in the final answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

The before evaluation was produced by `run_eval.py::main`, with retrieval from `store.py::search` and chunks produced by `chunker.py::split_documents`.

For the out-of-scope questions, the relevance gate refused 5 of 5. The best distances ranged from 0.819 to 1.018, all above the 0.60 cutoff.

All five in-scope questions produced the expected facts in every run and named at least one source document.

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

**Criterion 1 — MET.**  
The target was at least 4 of 5 test questions with a retrieved chunk containing the answer. All 5 questions had the answer available in the retrieved material across the runs, so this criterion was met.

**Criterion 2 — MET.**  
The target was for every answer to name at least one source document. All 5 answers named a source in every run, so this criterion was met.

**Criterion 3 — MET.**  
The target was for the relevance gate to stop at least 4 of 5 out-of-scope questions. The gate refused all 5 out-of-scope questions, so this criterion was met.

**Criterion 4 — MET.**  
The target was for at least 4 of 5 sampled chunks to read as complete thoughts with no sentence or word cut off at either end. All 5 sampled chunks met that standard, so this criterion was met.

**Criterion 5 — MET.**  
The target was for the system to use the correct retrieved information in at least 4 of 5 test questions. All 5 questions produced answers containing the expected fact in every run, so this criterion was met.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses
No criteria were missed in the before evaluation, so there was no failure to trace to a specific pipeline stage.

However, because every criterion passed on the first full evaluation, some of my original targets may have been too safe. The criterion I would tighten is Criterion 1. Instead of requiring the answer to appear somewhere in the retrieved chunks for 4 of 5 questions, I would make the target stricter by requiring the answer to appear within the top three retrieved chunks for at least 4 of 5 questions. This would test retrieval quality more directly instead of only checking whether the answer appeared anywhere in the top five.


 Milestone 3. -->

## The Improvement

**What I changed:**
I added hybrid search to `store.py::search`. The original retrieval used semantic similarity only. I kept semantic similarity as the main signal and added BM25 keyword matching so exact names, numbers, and terms in the question could also influence the ranking.

**Why I picked it:**
I chose this improvement because several of my questions depend on exact details such as "Kestrelford," "11am," "9pm," and "June." Hybrid search was a reasonable way to test whether combining meaning-based retrieval with keyword matching would improve how the most useful chunks were ranked.


<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunks contain the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks read as complete thoughts | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Retrieved information is used in the final answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |

**Did it help?**
The hybrid search did not change the overall criterion scores because all five criteria were already met before the improvement. However, it did change some retrieval rankings and source sets. For example, the exact Kestrelford food chunk ranked first for the pubs and cafés question after hybrid search.

The relevance gate also continued to refuse all 5 out-of-scope questions after the change.

Overall, the improvement did not increase the measured pass rate, but it preserved the existing performance while making exact keyword matches more influential in retrieval.

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken
No criterion was still missed after the improvement. All five criteria were met before and after hybrid search.

The main limitation is that my original criteria did not measure retrieval ranking very strictly. A correct chunk could appear anywhere in the retrieved results and still count as a success. Because of that, the hybrid search changed some rankings without changing my criterion scores.

If I continued working on the system, I would test whether the correct answer appears within the top three results instead of anywhere in the top five. I stopped here because Unit 2 asks for one measured improvement, and the hybrid-search change was completed and tested.

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently
I would make Criterion 1 stricter. Instead of checking whether one of the retrieved chunks contains the answer anywhere in the top five results, I would require the correct answer to appear within the top three results for at least 4 of 5 questions.

My original criterion was useful for checking whether retrieval found the answer at all, but it was not strict enough to measure ranking quality. The hybrid-search experiment made that limitation more obvious.

## How I Used AI section
In Unit 2, I used AI to help me compare my before-evaluation results against my five acceptance criteria and identify that all five criteria were met. Since there were no failures to diagnose, I used AI to pressure-test which criterion was too safe. That helped me identify Criterion 1 as a weak measure of ranking quality and choose hybrid search as a measured improvement.

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?



     Milestone 5. -->
