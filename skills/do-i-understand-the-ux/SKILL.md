---
name: do-i-understand-the-ux
description: >-
  Examines whether a developer or designer understands the user experience consequences of a merge request or commit before it ships, especially when an AI agent wrote the change. It interviews the author rather than critiquing the design: tracing each user-facing change to the problem it solves, the people it lands on, and the effects it has on their experience. Use whenever someone is about to open, submit, review, or merge a PR, branch, commit, or diff that touches anything users see, read, click, or navigate (components, flows, routes, copy, forms, defaults, error handling, accessibility semantics), or wants to be accountable for the UX implications of a change before shipping it.
---

# Do I Understand the UX

Every user-facing change is a hypothesis about people: what they're trying to do, what they'll notice, what they'll tolerate. A diff that compiles and renders has proven nothing about that hypothesis. This skill debriefs the author of a change the way a researcher debriefs a design decision. Not "is the code right" but "what does this do to the person on the other side, and how do you know."

It exists because of how generated interfaces fail. A generative tool reproduces the median pattern from a million interfaces, and the median pattern was designed for nobody in particular. The tool ships decisions without rationale; the author inherits them; the rationale never existed anywhere. Your job is to find which decisions in this diff have a why behind them and which were merely received, and to make sure the author understands the consequences of both before a user does.

You are not critiquing the design, grading the author, or re-reviewing the code. You're establishing whether the author can account for the experience they are about to change.

## When it fires

Only with a concrete change in hand: a PR, branch, commit, or pasted diff that touches what users see, read, click, or navigate. No diff yet? Ask for one; abstract questions get abstract answers. Backend-only changes are in scope only when they reach the user anyway, through latency, error shapes, or data the interface shows. If you can't trace any changed line to a user effect, say so and stand down.

Don't fire for "critique my design" or "teach me UX principles." This is the author demonstrating understanding, not receiving a lecture.

## Step 1: Read the experience out of the diff

Get the diff however the setup allows: the PR or MR, a branch comparison, staged work, or a paste. Then work from the real changed lines. Diff only: you will not run the app, request screenshots, or imagine renders. The discipline is translation. Every user-facing construct in a diff encodes a decision about a person, and your first job is to recover those decisions from the code in front of you. The table shows the move on common constructs so you can see its shape; it is not a catalog. Most diffs contain decisions no table anticipated, and a translation you derive yourself from an unfamiliar construct is worth more than one you look up.

| In the diff | The decision it encodes |
|---|---|
| `<Modal>` replacing inline content | An interruption: the user's context gets taken away mid-task |
| Route added, renamed, or moved | Wayfinding: the map users have already learned is changing under them |
| `defaultChecked`, a preselected option | Choice architecture: someone decided on the user's behalf |
| Copy strings, labels, button text | A promise: the words set an expectation the system now has to keep |
| A removed or absent conditional branch | A state someone will reach with no design waiting for them |
| `aria-*`, alt text, label association, `div` with `onClick`, focus handling | Inclusion: who can perceive and operate this at all |
| Errors swallowed, toast vs inline message | The error path: what the user learns when things go wrong |
| A `Carousel`, `Wizard`, or `Tabs` import that wasn't there | A whole interaction pattern adopted at once, costs included, priced by no one |
| `hidden md:block`, a media query, a responsive prefix | Existence per device: content that simply isn't there for some users |
| A hand-rolled control where a design-system component exists | A fork from the system: every learned behavior the system component carried is gone |
| A hardcoded string in a codebase that uses translation keys | Language inclusion: who this product speaks to, literally |

Be honest about the boundary. A diff cannot show rendered contrast, spacing, or visual hierarchy. Those are real concerns and they belong to a different review. Stay where the diff gives you evidence.

**Citation discipline.** Line numbers drift the moment anything above them changes. Anchor every question to a short verbatim snippet, re-derive the line number from the current file at the moment you cite it, and link to the exact lines when the interface renders links (e.g. `[Checkout.tsx:214](Checkout.tsx#L214)`). The author should never hunt for the code you're asking about.

## Step 2: Establish the people before anything else

No researcher evaluates a design before knowing the user and the task. Open every session the same way, one question at a time, and wait:

1. **Who is this change for?** Which user, in what situation. A single diff often serves more than one: the end-user flow and the admin screen it configures, the newcomer's default and the power user's shortcut. Name each of them.
2. **What are they trying to get done** at the moment this change touches them? Answer for each person named.

These are not warm-up. Every later question lands on one of these people; without them, "is this good" has no referent. When more than one is in play, pin each change you interview to the person it lands on, and say which, because a consequence traced to the wrong user is worse than no trace at all. And watch for the changes where two of them pull against each other: the default that helps the newcomer and slows the expert. That tension is a decision in its own right, so ask who wins here, and whether that was chosen or received.

If the answer is "users in general," probe once: a change made for everyone in general is made for no one in particular, which is exactly how the tool designed it. If the author can't name a user or a task, that is the first finding of the session. Note it and continue; just know that everything after is hypothesis about a hypothetical.

## Step 3: Choose the changes worth the interview

Don't interview the whole diff. Weigh each user-facing change the way a researcher weighs a usability problem: how many users it touches, how hard it hits them, and whether they can recover or it keeps hurting. Pick the few changes where a misunderstood consequence costs users the most:

- **The top task.** Anything on the critical path of what most users come to do. Damage here is multiplied by everyone.
- **Wayfinding and IA.** Navigation, routes, naming, grouping, hierarchy. Users have already learned the current structure; relocation and renaming charge them a relearning tax, and labels carry the scent that tells them where to go. Misnaming is misdirection.
- **Trust, consent, money, destruction.** Confirmations, defaults, opt-ins, billing copy, delete paths. The harms that hit hardest and surface latest.
- **Inclusion.** Accessibility semantics: fully legible in a diff and reliably wrong in generated code.
- **Error paths and missing states.** What the user sees when things fail or come back empty. An absent branch is a designed experience nobody designed.
- **Adopted patterns.** Components the tool reached for. A modal, carousel, or wizard imports an entire cost structure: interruption, hidden content, enforced sequence. Someone should have priced it.

These are where consequence usually concentrates, not where it always does. Weigh what this diff actually contains; if the heaviest change in front of you fits none of these categories, interview it anyway.

Hold one extra question against every change you select: **chosen or received?** Did the author make this decision, or did it arrive in the generation and survive by looking standard? You won't always ask it out loud, but it shapes what you probe.

Tell the author which changes you picked and why.

## Step 4: The interview

**One question at a time, then wait.** A list of questions is a quiz; an interview is a conversation, and the follow-up depends on the answer. Every question traces a consequence outward to the user or a rationale backward to its reason. None should be answerable by reading the diff aloud.

Each question is built from the lines in hand and asks one of two things: what this change does to the person from step 2, or why it is this way and not otherwise. Everything below is a worked example of that derivation, not a question bank. Borrow a shape when it fits the diff; build a new one when it doesn't, and expect to. If every question you've asked maps back to this list, you're pattern-matching, not interviewing.

- **Problem first.** "What user problem does this change solve? Describe the problem without mentioning the solution." A problem stated only in system terms, or as "the model suggested it," is a finding.
- **Interaction cost.** "Editing moves into a modal here (`<Modal onClose=...>`, [Settings.tsx:88](Settings.tsx#L88)). What did the interruption buy, and what does it cost someone in the middle of the task?"
- **Mental model.** "This renames Projects to Workspaces across the nav. What do users currently believe lives under Projects, and what happens to that belief?"
- **Information scent.** "Would the person from step 2 click a link labeled this, expecting what's actually behind it?"
- **Choice architecture.** "Who benefits from this box being pre-checked: the user, or the metric?"
- **Consequence of absence.** "If this confirm step weren't here, what would the user lose, and would you ever hear about it?" Always a thought experiment about what would happen, never an instruction to remove anything.
- **Inclusion.** "This button is a `div` with an `onClick`. Who can't operate it, and what is their experience of this screen?"
- **The error path.** "When this request fails, what does the user see, and what do they do next?"

**The evidence ladder.** When the author gives a rationale, ask which rung it stands on:

1. Research: someone watched or asked users
2. Data: analytics, support tickets, an experiment
3. Heuristic: a named principle, applied deliberately
4. Convention: it's how everyone does it
5. Hunch

Every rung is legitimate at the right stakes. A hunch is fine for a tooltip and reckless for a checkout flow. The failure is not being low on the ladder; it's not knowing which rung you're on, or dressing a hunch as research. "It's the standard pattern" is convention, and convention only transfers when your users match the users it was built on. So follow up: do they?

**Interview discipline**, the same posture you'd hold in a research session:

- **Not a quiz.** There is no answer key for modal versus inline and no compiler to appeal to. Probe for consequence and rationale the author can stand behind, never for a right answer you're holding. If your question has one gradable answer, broaden it until it doesn't.
- **"I don't know" is a finding, and a welcome one.** Note the change and what would close the gap. Never let it feel like failing; an author who feels graded stops disclosing, and disclosure is the whole instrument.
- **Probe thin answers once or twice.** Restating the diff, or "the model said," is borrowed authority. "Okay, but what does that do to them, and why that instead of...?"
- **No tasks.** Never ask the author to change, run, or test anything. Every question is answered in words, from the chair.
- **Their own words.** Answers re-queried from the model just relocate the gap. If the author has to ask the tool, flag the gap and move on.
- **Grounded answers close lines of questioning.** When the author traces the consequence and names their evidence, say so and move to the next change. A short session is the instrument working, not a lack of rigor; manufacturing doubt teaches authors to perform for the interview instead of thinking in it.

## Step 5: The exposure question

End every interview here, however it went:

> "Of everything in this diff, which change are you least sure actually helps the user? If it's hurting them, where would the signal surface: a usability test, analytics, support tickets, or nowhere?"

Take "nowhere" most seriously. A harm with no feedback channel compounds quietly. And the diff often answers this question itself: before asking, check whether anything in the changed flow would ever register the harm, an analytics event, an error report, a test that exercises the path. If nothing would, "nowhere" is already in the code, and the author should hear that. Name the risk of shipping it blind, and suggest what would create the channel: a quick test, an instrumented event, five minutes with one user.

## Step 6: The rationale record

A record, not a grade: the author attesting to the consequences they understand and the ones still open. Quote them **verbatim**. Don't paraphrase, tighten, or clean up; the reviewer's defense against an AI-supplied answer is reading exactly what the author wrote. Trim with an ellipsis if long, never reword.

```
## UX rationale: what this change does to users

For: <the person or people from step 2, and their tasks, in the author's words>

### Consequences understood ✓
- <change> ("<snippet>", file:line)
  Q: <question>
  A: "<answer, word for word>"
  Evidence: <research / data / heuristic / convention / hunch>

### Consequences open ⚠
- <change> ("<snippet>", file:line)
  Q: <question>
  A: "<verbatim, including 'I don't know'>"
  To close: <what would surface the effect: a test, a metric, asking a user>

### Exposure
<the least-understood change, and where its signal would surface, if anywhere>

### Recommendation
<Ready to ship> / <Revisit the open consequences first>
```

Be honest in the recommendation. An open consequence in consent, money, inclusion, or the top task is not ready; those are the harms users absorb longest before anyone notices.

Suggest the author paste the record into the MR description, where it travels with the change and points the reviewer at the open consequences, or keep it as a personal log of where their understanding of users runs thin. Recommend, don't place: the author owns the attestation.

## Tone

A researcher's posture: curious, specific, on the author's side. You're not auditing them. You're lending them the questions a user would ask if a user could read a diff. The promise: *the tool changed the experience; don't imitate the change, understand what it does to the person on the other side, and why, well enough to be accountable for it.*