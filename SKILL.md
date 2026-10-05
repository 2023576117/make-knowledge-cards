---
name: make-knowledge-cards
description: Turns a pasted article or a local Markdown/TXT file into knowledge cards, each covering exactly one idea with a title, the core point, a plain explanation, and either an example taken from the source or a self-test question. This skill should be used when the user asks to turn an article, essay, blog post, note, or documentation into cards, flashcards, 卡片, 知识卡片, or 读书笔记. It also declines the inputs that cannot support cards, such as diaries, transcripts, and logs. It does not fetch web pages, read PDFs, export to Anki, or provide a graphical interface.
license: MIT
agent_created: true
---

# Make Knowledge Cards

## Overview

Convert one source text into a small set of self-contained knowledge cards. Each card carries a single idea, states that idea in the source's own terms and no stronger, and makes it checkable by the reader.

When the source does not support cards, say so. Reporting that a source has no extractable knowledge is a correct result, not a failure.

## Scope

**In scope**

- Text pasted directly into the conversation.
- A local `.md` or `.txt` file path supplied by the user.

**Out of scope — decline and state the limitation instead of improvising**

- Web page fetching or URL reading. Ask the user to paste the page text or save it as a local file.
- PDF, DOCX, EPUB, images, or scanned documents. Ask the user to paste or convert to text.
- Anki/CSV export or any flashcard-app integration. This skill produces Markdown only.
- Any graphical interface. This skill runs entirely in the conversation.

If a request mixes an in-scope input with an out-of-scope one, handle the in-scope part and name what was skipped and why.

## Workflow

### Step 1: Obtain the source

- If the user pasted text, use it as-is.
- If the user gave a file path, read the file. If the extension is not `.md` or `.txt`, ask for a text version rather than guessing at the content.

Read the entire source before deciding anything. If it is too long for one pass, read it in consecutive chunks, inventory each chunk separately (Step 2), then merge the inventories before selecting. A mechanism split across two chunks is one candidate, not two. Never build cards from the first screenful.

### Step 2: Inventory candidate knowledge points

List every claim the source makes — mechanism, constraint, rule, conclusion, or judgment — including ones that appear only in passing. Note the source's structural anomalies while reading: headings missing a space after `#`, conclusions written as list items, sections with no heading. **A heading is not a knowledge point.** When a heading is itself a normative claim ("X should beat Y"), treat it as the subject under discussion and look to the section body for the mechanism or measurement supporting it; that supporting content is the candidate.

Sort each candidate into exactly one bucket:

- **Load-bearing** — a mechanism, constraint, rule, or judgment the rest of the source leans on. Test it by deletion: removing it would leave later statements unsupported.
- **Supporting** — real content that also carries a point a reader could carry away: a rule, a stated cause, a judgment criterion, or a worked instance of one of those. A one-off scene with no transferable point is Incidental even when plainly true.
- **Incidental** — asides, tone, motivation, restatement, meta-commentary.

**Judgments are first-class candidates.** Opinion and experience essays are made of them, and dropping them empties the source. A judgment is a substantive position the source takes — a normative claim or a stated preference, typically carrying a first-person or normative marker ("I", "my", "in my case", "should", "ought", "I find", "I chose"). A candidate with no such marker is a mechanism, not a judgment. When a judgment is kept, its marker is kept in the same sentence; see Overreach in Step 6.

**The judgment label does not override the Incidental bucket.** A first-person marker inside a one-off scene with no transferable point is narration, not a judgment: "I said yes", "I was forty-three that year" stay Incidental. Ask whether the source is taking a position or merely reporting that something happened.

Keep this list for your own use. Report only the tallies ("10 load-bearing, 4 supporting") unless the user asks which points were selected or how they were grouped; answer that in full when asked.

### Step 3: Select what becomes a card

Keep load-bearing candidates. Add a supporting candidate only when it carries a mechanism, constraint, rule, conclusion, or judgment that no retained candidate already covers. Drop incidental material.

Then apply two filters:

1. **One card, one idea.** A candidate is one idea if a reader could drop that card without losing the rest of the set. A card needing the word "and" to summarize itself is two ideas — split it, and **one-idea wins over the card count.** One exception: parallel applicability conditions ("fails when A, costs less when B") are the complete meaning of one boundary, so keep them on one card rather than splitting into cards that reference each other.
2. **Deduplicate.** Merge candidates that say the same thing in different words. If two candidates would produce cards a reader could not tell apart, keep the clearer one and fold any genuinely distinct detail from the other into it as a single line.

### Step 4: Decide how many cards to write

Aim for 5–8. Treat that as a target, never as a quota. Resolve the branches in this order.

1. **Nothing survives, or fewer than 3 candidates survive and all are single-sentence incidents with no transferable point.** Do not write cards. Diaries, transcripts, logs, and chat records usually land here, as does a source whose only survivors are first-person narration with no position taken. Report `Cards: 0` with the Note line explaining what the source is and why it yielded no independent knowledge points, then offer either a source that would support cards or a different treatment such as a summary or a timeline. If the user still wants cards anyway, build them with the normal `Cards: <N>` count and set `Note:` to record that they were built on request and fall below normal density.
2. **Fewer than 5 candidates survive.** Write only those. Never pad and never split one idea thin to hit a number. Report the count and the reason.
3. **Honest splitting pushes the set above 8.** Write them all. Do not truncate, and do not merge ideas to get back under the ceiling. Note the excess.
4. **More than 8 candidates survive and no splitting is involved.** Write 8, ranked by: applicability limits the source states for its own load-bearing claims (drop them and the remaining cards read as universal rules); the failure modes and constraints a reader would otherwise get wrong, since a tutorial that omits them teaches the mistake; mechanisms another card depends on; named mechanisms the source gives its own version numbers, flags, or pragmas for; everything else. Within one tier, prefer the candidate that more of the source builds on. Cut from the bottom of that ranking. Prefer folding a cut candidate's detail into a retained card over discarding it, and name every omission and fold in the closing line.

### Step 5: Write each card

Fill all four fields.

- **Title** — one short claim, or a short noun phrase. Name the problem, not its whole mechanism chain. "Replica lag breaks cache invalidation" beats "About caching"; "The author prefers flat search over tags" beats "Tags are worse than search".
- **Core knowledge** — the point itself, in one or two sentences, in the source's terminology and at the source's strength, understandable on its own. A judgment keeps its first-person or normative marker here.
- **Explanation** — why it holds or how it works, in words different from the core knowledge. Fill this field **only** with reasoning the source entails:
  - restating a cause the source names,
  - spelling out a consequence the source states,
  - connecting two facts the source itself puts in the same passage.

  Do not add a mechanism, causal claim, category, or quantitative statement the source never states. Apply this test to every sentence: *could this sentence be expressed using only the source's own words and its own connective logic?* An invented taxonomy ("there are two kinds of solution") fails; a restated cause passes. "In other words, the same thing stated differently" fails.

  Two situations where a thin Explanation is correct, not a shortcut:
  - **The source states a result without giving its mechanism.** Write the source's own framing — what it calls the result, what it contrasts the result with — and stop. Do not invent a mechanism and do not pad with restatement.
  - **The card is a boundary or exception.** Describing how the source frames the boundary — which section it sits in, whether the author calls it an exception or a reversal, whether it is scoped to a named condition — satisfies this field. Borrowing a mechanism from elsewhere in the source to fill it does not.
- **Example or self-test** — include exactly one, never both:
  - **Example** — when the source supplies a worked case: a described situation with a concrete instance, outcome, or quantity attached. Quote or closely paraphrase it, and carry over the source's hedges and markers ("in the author's own count over one year"). Never invent an example. A method or system name alone is not a worked case, and neither is a bare parameter, threshold, or version number — those are the idea, not an instance of it. A first-person statement of the author's own current practice ("all my notes are now plain text") is the core knowledge restated, not a case.
  - **Self-test** — when the source supplies no worked case, when the idea is a rule the reader must apply (a condition, a threshold, a constraint) rather than an event they can picture, or when the source's only instance merely restates the card's own claim. Ask something the card itself answers. Keep the answer recoverable from the card's own text; do not print it separately.

### Step 6: Verify before delivering

Check every card and fix or drop any that fail:

- **Fabrication** — does any claim, number, name, or causal connection appear nowhere in the source? Remove it. Restating something already said in another card is fine; a new fact is not.
- **Overreach** — does any sentence claim more than the source claims for it? Three forms to catch: **attribution loss**, where the source says "I / my / in my case" and the card states it as a general fact; **modality inflation**, where the source says "I chose / I find / should" and the card says "will / is / does"; **evidence inflation**, where the source reports one person's unsystematic count and the card presents the numbers as a measurement. Restore the source's marker. If a sentence cannot hold its marker without becoming false, cut it. This check catches the most common failure on opinion and experience sources, and it is invisible to Fabrication because no new fact appears.
- **Duplicate** — could a reader tell cards 3 and 6 apart? Merge them.
- **Multi-idea** — does the card need an "and" to summarize itself? Split it, unless it is one boundary's parallel conditions.
- **Restatement** — is the Explanation just the Core knowledge in other words? Rewrite it around mechanism or consequence. The two thin-Explanation allowances in Step 5 are exempt: a result the source states without a mechanism, and a boundary or exception, may keep its Explanation as the source's own framing. Also check the reverse: does the Example add anything the Core and Explanation did not already say? Switch it to a self-test if not.
- **Orphan** — is the self-test question answerable from that card alone, without the source? Rewrite it if not.
- **Source conflict** — does the source contradict itself? Keep the conflict visible and attribute both halves to the source rather than silently picking a side. Note it in the closing line.

## Output Format

Produce Markdown in this shape. Write the cards inline in the reply; write to a file only when the user asks for one.

Field labels stay exactly as written below in every language. Card titles and all field content match the source's language, and the H1 reproduces the source's own title or first-line topic in the source's language.

```markdown
# Knowledge Cards: <source title or first-line topic>

Source: <"pasted text", or the file path exactly as the user gave it>
Cards: <N>
Note: <one line — required when the count is outside 5-8, when Cards is 0, or when any card rests on fewer than two source sentences>

---

## Card 1 — <title>

**Core knowledge**: <one or two sentences>

**Explanation**: <why it holds, in other words>

**Example**: <the source's own worked case>
```

Repeat the card block for each card, substituting `**Self-test**: <question answerable from this card alone>` for the `**Example**` line where that is what was used. Use exactly one of the two labels per card.

When at least one card was written, close with one short line naming anything deliberately left out or folded into another card, and why:

```markdown
---

Omitted: <what was dropped or folded, and why — omit this whole line when nothing was dropped and nothing was folded>
```

`Cards: 0` is valid: write the header, the `Source` line, `Cards: 0`, and the `Note` line, write no card block, and add no closing line. The offer of an alternative treatment that Step 4 branch 1 requires still goes there, in place of the cards.

Match the source's language by its actual content, not by the file name, the path, or the user's description. If those imply a different language than the text, follow the text and note the mismatch in the closing line.
