# Design

English | [日本語](design.ja.md)

> The Japanese version is authoritative. If the two versions differ, follow the Japanese version.

The design of the kit, as part of the basic design of legacy-lab. How to write the timeline is in [timeline-format.md](timeline-format.md), and the requirements are in [Requirements](../REQUIREMENTS.md). Chapter numbers are kept as the agenda numbers used when the basic design was decided (chapter 1 is the timeline format). Stage 1 to stage 5 refer to the five stages in the requirements (stage 1 Conditions, stage 2 World, stage 3 Timeline, stage 4 Implementation, stage 5 Packaging). A date in parentheses is the date the rule was decided.

## 2. How company events are created at stage 3

The company timeline is built by connecting the company's business, people, and constraints with verified world events. At stage 3 a human checks the overall framework, and at stage 4 the fine details of the period materials are added to fit the previous implementation (decided 2026-11-11).

### Five policies

| Question | Decision |
| --- | --- |
| Where events start | Connect the company's business, people, and constraints with world events, and explain who has what problem and why the system is changed. Events that happen only inside the company, with no world event involved, can also be created |
| When details are created | At stage 3, settle the events, dates, phases, work dates, business purpose, scope, and usage environment. At stage 4, look at the previous implementation and add the details of the period |
| How phases are divided | Divide by units of change that can be handed over as business work. Show the purpose, what is changed, what is left untouched, and the relation to the previous phase |
| What the Respondent knows | For each person, prepare their area of responsibility, what they know up to the work date, and materials they can hand over when needed. Outside that range, they answer "I don't know" |
| Amount of period materials | Prepare request memos, the minimum necessary real artifacts (such as CSV files), and the people and environment of the period. CSV and text are the default |

### Flow from stage 2 to stage 4

1. Stage 2: build the world. From the stage 1 language, era, industry, country, and scale, create the fictional company, people, departments, business, existing systems and materials, the Developer's experience, and the tool versions the company uses. For each person, set what they know and what they do not.
2. Stage 3: choose the material. Take verified world events that match the country, industry, and era as candidates, and narrow them by areas to fit the business decided at stage 2 (areas is not added to the stage 1 conditions). Record the timeline version used and the row ids.
3. Stage 3: create company events. Check each one as "trigger → who has a problem → the company's response → business change → what is left untouched this time." Tie things such as growing transaction volume, more sites, a change of the person in charge, exception conditions, audits, and double entry to the company's setup. Do not fabricate events to fit an ending of bad code.
4. Stage 3: split into phases and show a human. Export a one- to two-page timeline from SQLite. Lay out the period, world events, company events, purpose of the change, phase, and usage environment, so that the difference between the known date and the occurred date can also be read. Details of people and materials go in an appendix; apply corrections before moving on to stage 4.
5. Stage 4: make the period materials concrete. Add only behavior, samples, and points to raise that actually exist in the previous implementation, and explanations based on knowledge at that time. If there is no difference, write "no difference." Do not create bugs that do not exist, or material that hints at the future.

A contradiction that cannot be fixed without changing the stage 3 framework goes back to a stage 3 revision, with the cause and the difference recorded. The human checkpoints are normally stages 1, 3, and 5; no per-phase approval waits are added. The HTML is for viewing; approvals and change requests are received through the conversation with the user's agent.

### How company events are held

- The rules for time and verification follow the [Timeline format](timeline-format.md). Company events use the same framework (known, occurred, stage, thread). They are stored separately from world events, while the processing that extracts by date and thread (the index script) is shared.
- Information that belongs only to the company (people, business, references to world rows, phase, and so on) is held as additional information.
- Checking that the fictional setup fits together is kept separate from marking real-world facts verified. Do not attach fabricated source URLs to company events.
- A rumor is stored as a company event: "a rumor reached the company." The content of the rumor is not treated as a verified fact, and no stage value is added for rumors. Confirmed reports and changes in response are appended as separate rows.
- Even if something happens after the work date, it may be shown if it was known by the work date. Knowing about something and actually being able to use it are kept separate (whether it can be used is covered in the next section).

### The company's tool versions, and from when they could be used

- The company's tools are held with the same names (csharp, vbnet, and so on) and versions as tech.product in the world-event timeline. Record the event in which a tool was adopted and the id of the row used as the basis for its start of availability.
- The basis is an effective row for the same product and version that marks the start of availability, such as a release or general availability, that is verified and known as of the work date. Among the candidates whose conditions (scope of availability, contracts, and so on) the company meets, take the earliest occurred. Rows for announcements, planned ends, and end of support are not candidates.
- Choose candidates after checking the latest state of each thread as of the work date. Later changes or cancellations are not carried back into the past.
- A tool can actually be used when "start of availability ≦ work date" and "start of adoption at the company ≦ work date." A release alone does not change the company's environment.
- At stage 2, record the id of the start-of-availability row explicitly. Do not decide it automatically just by searching the text of title.
- If the basis is insufficient, the conditions of availability are unclear, there is a contradiction on the same date, or the date is too coarse to fix a start date, do not assume the tool can be used. Mark it "needs checking," carry it over to stage 3 without waiting for a human at stage 2, and present such items together in the timeline shown to the human at stage 3. The human checkpoints stay at stages 1, 3, and 5; none are added. Anything that comes up at stage 4 goes back to a stage 3 revision, as before.
- An occurred given only to the month or the year is kept as it is, without filling in an actual day. The tool is not treated as usable from partway through that period.

### Hand over only what belongs to the period

- The work date is set by the lab. After filtering by date, filter further by each person's responsibilities and what they know. References and materials handed over when needed pass through the same boundary.
- The source DB, the whole timeline, future materials, and the Scriptwriter's internal notes are never passed to the workspace folder of the Developer and the Respondent. Check the list of distributed files, dates, references, and scenario contamination before starting the Developer.
- The Developer records decisions, reasons, options considered, and the scope of impact in the decision notes. At stage 5, the check report and the build results are shown to the human, and the code, timeline, and decision notes are bundled as one set.

### What to measure on the first run

Whether the target of one to two days is reachable, and the limits on the number of phases, the amount of materials, the number of questions, and AI usage, have not been measured yet. They are measured on the first run.

## 3. What the Inspector may see

The Inspector looks from outside the work date. In exchange for being allowed to see into the future, the work-date boundary is protected by how findings are handed over. The check is done at the end of each phase, on that phase's diff (decided 2026-11-11).

### Three policies

| Question | Decision |
| --- | --- |
| What may be seen | The whole world-event timeline, the whole scenario (stage 2 world and stage 3 timeline), the code and decision notes of all phases, and the results of the machine check |
| Where reports go | Split into two: a report for humans and a fix request for the Developer |
| When it runs | Per phase. At the end of each phase, look only at that phase's diff |

### What may be seen

- The work-date boundary exists to keep the future out of the hands of whoever writes the code. The Inspector does not write code and only reads it, so the Inspector is not confined inside the boundary.
- The Inspector's main job is to find the future mixed into past code. The Developer's AI knows the future from its training, so it can mix it in by accident (e.g. code in a 2015 phase that handles the Japanese calendar in preparation for Reiwa).
- Each finding states its basis: the id of the timeline row (an event known after the work date) or the company's tool version.
- The Inspector does not fix code, and does not talk with the Developer or the Respondent.
- The Inspector's conversation is never reused for the Developer or the Respondent, so that a conversation that has seen the future does not get inside the work date. How conversations are separated is left to the user, so this is enforced by the written procedures.

### Where reports go

- The report for humans may include everything: which line of which file in which phase does not fit which timeline row or company tool version, and whether it was fixed or left as it is.
- The fix request for the Developer is written only in words from inside the work date. Names and dates of future events are not written (e.g. "Do not add era names that are not in the materials of the work date; build only from what is in the materials").
- A fix request goes to the Developer only after passing chapter 2's check of "the list of distributed files, dates, references, and scenario contamination." No new gate is created.
- Anything that cannot be fixed without changing the framework goes back to a stage 3 revision, as in chapter 2.

### When it runs

1. When a phase ends, apply the machine check (markers).
2. The Inspector judges the spots the machine picked up, and checks that phase's diff for the feel of the era and for any future mixed in.
3. If anything needs fixing, hand a fix request to the Developer, and fix it within that phase.
4. The Inspector looks again only at what was fixed.

- At most one round trip of fixes. Anything that remains after the second look is left as it is and written in the stage 5 report. This keeps the check from running on endlessly and inflating AI usage.
- At stage 5, a script gathers the per-phase reports and shows them to the human. The human checkpoints stay at stages 1, 3, and 5; none are added.
- Whether a final end-to-end check of the whole is needed, and how much AI the Inspector uses, are measured on the first run. If it is too heavy, change to a form such as reviewing everything together at stage 5.

## 4. Repository structure

The distributed kit and the timeline live in the legacy-lab repository. The run folder for each system is outside the repository, and each output is a separate repository. After this chapter, the basic design moves to docs/ in the repository and becomes the authoritative version (decided 2026-11-12).

### Six policies

| Question | Decision |
| --- | --- |
| How locations are split | The repository holds only the distributed kit and the timeline. Run folders are outside the repository. Each output is a separate repository |
| Repository folders | The kit is a single Python package (legacylab/), called with `python -m legacylab <task>` |
| Run folder sections | Seven: script, control, work, answer, review, preview, output |
| Document location and language | The basic design moves to docs/ and becomes authoritative. Japanese is authoritative, with English alongside. The instructions are a single set in English |
| Automatic pull request checks | The check is a legacylab command, and CI only calls it |
| License | The root is MIT, data/ is CC BY 4.0. Both are stated in README and CITATION.cff |

### Repository folders

```
legacy-lab/
  README.md              The entry point agents read first
  REQUIREMENTS*.md       Requirements
  data/events/           The authoritative timeline
  legacylab/             The Python kit (standard library only)
  instructions/          Instructions for the Scriptwriter, Developer, Respondent, and Inspector
  templates/             Templates for the output's README and build
  tests/                 Tests for the kit
  docs/                  Rule documents
  .github/workflows/     Automatic pull request checks and tests for the kit
  LICENSE・CITATION.cff  License and citation
```

- The work-date boundary rules live in one place in legacylab/, and the index, the export to the workspace folder, the contamination check, the fix-request check, and the HTML for humans all use them. If the same rules were written several times, a single mismatch would leak the future.
- No extra installation is needed. The requirement for "small scripts" is met by keeping the command for each task small.

### Run folder

A run folder is created outside the repository, one for each system (e.g. `legacy-lab-runs/<system-name>/`). Its location is given to the kit through settings. This keeps the whole timeline and the scenario out of the Developer's reach. A place that is out of reach is more reliable than a promise not to look.

| Section | Contents | Used by |
| --- | --- | --- |
| script | The scenario (all of stage 2 world and stage 3 timeline), the source SQLite | Scriptwriter. The Inspector only reads it |
| control | Conditions, work date, what was adopted at stage 3, measurements | The kit (legacylab). The Developer and the Respondent cannot change the work date |
| work | The table filtered by work date, period materials, code, decision notes, questions | Developer |
| answer | The Respondent's materials and answers for each person | Respondent |
| review | Reports for humans and drafts of fix requests | Inspector |
| preview | A single HTML page for humans | Humans (stages 1, 3, and 5) |
| output | The output bundled at stage 5 | Packaging |

- Every exchange between sections passes the boundary check: the Developer's questions to the Respondent, the Respondent's answers into work, and the Inspector's fix requests into work as well.
- Each AI is started with its own section as its working location.
- answer is kept separate from work so that the Developer cannot read the Respondent's "materials they can hand over when needed" without asking. Unless the materials pass over only when asked for, the exchange does not feel real, and the number of questions to be measured on the first run cannot be measured.

### Document location and language

```
docs/
  timeline-format.ja.md / timeline-format.md   The timeline format (chapter 1). The entry point for people sending pull requests
  design.ja.md / design.md                     The design of the kit (chapters 2 to 4)
```

- The Japanese version is authoritative, and the top of the English version says that the Japanese version is authoritative. The timeline format is a separate file so that people sending pull requests need to read only that file. The same rule is not written in two places.
- The move happens after chapter 4 and before implementation. Rule changes usually happen together with code changes, so the rules are also changed in the repository.
- The instructions (instructions/) are a single set in English. AI agents act exactly as the instructions say, so with two sets, any gap between them would become a gap in the quality of a system. The instructions say: "Write screen text, comments, materials, and decision notes in the language of the country chosen at stage 1."

### Automatic pull request checks

The check is `python -m legacylab check`, and CI only calls it. The check is the same locally and on pull requests, so people sending pull requests can run it before they send.

| Group | What is checked |
| --- | --- |
| File | One JSON object per line; the year of the file matches the year of known |
| Fields | Required fields are present; kind and stage have the allowed values; areas are among the 10 terms; industries are Rev.4 codes (A to U, lower-level digits allowed) or `*`; tech is present on rows that need it; markers are not on two or more rows for the same version |
| Dates | The format of known and occurred (an "empty" occurred is `null` with the key kept); the date rules for each stage |
| id | Format, and no duplicates |
| Reliability | One or more sources with no tracking parameters; verified has on and by; no row whose fact fields changed while it stayed verified (compared with before the pull request) |
| commit | The author and committer of the maintainer's commits are the noreply address |

- The commit check looks only at the maintainer's commits; it does not look at other people's email addresses. It is built so that the maintainer's private email address is not written in a public configuration file. GitHub's push block looks only at the latest commit, so this check looks at all commits (on 2026-11-11, the email address of the maintainer's account was made private and that push block was turned on).
- The kit's tests run on three platforms: Windows, Mac, and Linux. The build check for a finished system (on a Windows machine) goes into the templates in templates/.

### License

- The root LICENSE (MIT) stays as it is. A CC BY 4.0 LICENSE is placed in data/, and both are stated in README and CITATION.cff.
- For people sending pull requests, it says: "Rows contributed to data/ are deemed to be submitted under CC BY 4.0, and contributions to the kit under MIT" (at the end of the [Timeline format](timeline-format.md)).
- The timeline data was first published under the root MIT license. Before any pull request from other people was merged, it was changed to CC BY 4.0 in the same pull request that moved the design to docs/.
- A finished system includes timeline rows as part of its provenance, so the README template in templates/ has a field for the attribution of the source (CC BY 4.0).
