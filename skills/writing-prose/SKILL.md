---
name: writing-prose
description: Draft or edit prose for human readers, such as docs, READMEs, posts, essays, emails, reports, commit messages, and PR descriptions. Use for new text and for proofreading, tightening, or rewriting. For agent instructions or LLM prompts, use writing-for-agents or writing-prompt.
---

Deliver the finished text first. Then add brief notes: assumptions, open placeholders, unverified claims, and check results.

## Before you draft

- Infer the reader, purpose, genre, format, length, and voice from the request and the workspace. Ask only when a wrong guess would waste the draft.
- Outline a long piece before you write the body.
- Non-technical text (posts, essays, emails, letters, talks) follows its genre and the user's voice, unless the request names another voice. Only the all-text Style rules apply to it.
- Learn the voice from the user's own samples in the workspace or the conversation. If none exist, write plainly and say so.

## Drafting

- Put the main point in the first paragraph of docs, emails, and reports.
- Use concrete detail (names, numbers, examples, code) instead of abstract claims.
- Cut filler: warm-up openers, stacked hedges, stock phrases, and summaries that repeat the body. Avoid reflexive triplets and "not X, but Y" contrasts.
- Do not invent facts, quotes, numbers, sources, or personal experiences. Mark each gap with a placeholder, such as `[TODO: launch date]`.
- Verify each factual claim by the evidence rules in Coding discipline. In a summary, keep each claim at its original strength.
- For new docs, match the format of neighboring pages. Give each section one Diataxis job: tutorial, how-to guide, reference, or explanation.
- Match recent commit style (`git log --oneline -20`). In commit messages and PR descriptions, state why the change exists. Use the repository's PR template when one exists.

## Editing

- Go only as deep as asked. A proofread fixes errors only. A request to tighten or improve allows sentence edits. Restructure only on request.
- Keep the author's meaning, voice, terms, and formatting. Flag each claim you think is wrong, and leave it unchanged.
- After the edited text, list each change to meaning or structure.

## Before delivery

1. Run each code sample and command in the text, within Authorization.
2. Check links with `lychee <file>`, or `lychee --offline <file>` for local links only.
3. Scan for banned punctuation and emojis: `rg -n '[\x{2013}\x{2014}\p{Extended_Pictographic}]|\S - \S' <file>`.
4. Read the full text once as its reader. Cut what that reader does not need.
