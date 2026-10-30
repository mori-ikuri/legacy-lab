# legacy-lab Requirements

English | [日本語](REQUIREMENTS.ja.md)

- Status: Approved (2026-10-30)
- Next phase: Basic design

## 1. Purpose

legacy-lab is a general-purpose kit that reproduces a legacy business system from a set of conditions: language, era, industry, country, and scale.

- The lab's job ends at reproduction. What the reproduction is used for (testing migration tools, training, teaching) is up to the user.
- Each output is a pair: **code + provenance** (an event timeline and decision notes).
- Target: one system in one to two days of work (exact figures are measured on the first run; see section 11).
- legacy-lab generalizes the hand-made method of [AI-LMC](https://github.com/mori-ikuri/ai-lmc). AI-LMC serves as the first sample and as the yardstick for the kit's quality.

## 2. Intended users

| Group | Who |
| --- | --- |
| Primary | People and organizations who want to test migration tools or AI-driven modernization, and need legacy code they can check results against |
| Future | People who want to practice maintaining legacy code, and people who teach it |
| Out of scope | People who need AI research or evaluation material that does not have to be legacy |

Users are assumed to be able to run an AI coding agent (Claude Code, Codex, and similar).

## 3. Form

- A set of instruction files plus small scripts. Deterministic steps are done by scripts; thinking work is done by the user's own AI agent.
- Scripts use Python with the standard library only. Nothing extra to install.
- The user asks their agent to "build one with these conditions," and the agent calls the scripts.
- The lab never calls an AI API itself (no API keys, no pay-per-use billing).
- Usable from the README alone.

## 4. Flow (five stages)

1. **Conditions**: choose language, era, industry, country, and scale.
2. **World**: from the conditions, create the company, people, business, and the technical environment of the time (including the tool versions the company uses).
3. **Timeline**: lay out the business events that happen to the company and split them into phases.
4. **Implementation**: the Developer builds each phase using only information available up to the work date, and leaves decision notes (with a limited number of questions to the business side).
5. **Packaging**: bundle the code and its provenance into a repository.

## 5. Roles and separation

| Role | What it does | What it knows |
| --- | --- | --- |
| Scriptwriter | Creates the world (2) and the timeline (3) | Everything, including the company's future |
| Developer | Writes the code in stage 4 | Up to the work date |
| Respondent | Answers the Developer's questions as the business side | Up to the work date |
| Inspector | Checks the finished output (section 8) | Decided in basic design |

- How conversations are separated (different tools, or separate sessions in one tool) is up to the user.
- What the kit enforces is placement. The Scriptwriter's output lives in a **scenario folder**. The **workspace folder**, where the Developer and Respondent run, contains only the table exported for the work date.
- Before the Developer starts, a script checks that no events after the work date are present and that no file from the scenario folder has slipped in.

## 6. Output guarantees and first-release scope

Each output states its guarantee level. Languages with available build tools default to **Buildable**.

| Level | Guarantee |
| --- | --- |
| Readable | Reads naturally as code from its era. Builds are not guaranteed |
| Buildable | Builds with free tools available today |
| Runnable | Ships with sample data; screens and reports actually run |

Example: VB.NET and C# can be verified up to Buildable. VB6 stops at Readable because its original IDE is no longer obtainable.

### Quality scope of the first release

- Japan × .NET (VB.NET and C#, from 2002 when .NET Framework shipped). Industry and scale are free within this range.
- Reason: the maintainer can judge authenticity in this range, and builds can be verified with free Visual Studio.
- The kit itself accepts any country, language, and industry. Ranges outside the first release are for later versions, together with a way to judge them.

### Not in scope

- Verifying modernization (migration). Trying a migration is the user's job; the provenance lets users check their own results.

## 7. Timeline

The timeline is indexed with SQLite, and the Developer receives only the events that were known as of the work date.

### Contents

- Events are split into **world events** and **company events**. World events are reused across systems from the same country and era.
- Each event has at least two dates: a **known date** and an **effective date**. Example: Japan's consumption tax increase to 8% was enacted in August 2012 and took effect in April 2014.
- The collection criterion is whether the event affected business systems. Each event records which countries and industries it affects, so it can be filtered by the conditions from stage 1.
- Field definitions are decided in basic design.

### Indexing rules

- The index returns events **known as of the work date**. The effective date is read as part of the content.
- The work date is set by the lab. The Developer and Respondent cannot change it.
- The table filtered by work date is exported to a file separate from the source database, and only that file is placed in the workspace folder. The source database stays outside the workspace folder.

### Source of truth and contributions

- The source of truth is plain-text files per country and per year, one event per line. Scripts build the SQLite database from them, so that Git diffs stay readable and pull requests are possible.
- The maintainer builds the Japanese events first as a reference for the format. Events for other countries are welcome as pull requests.
- Pull requests are checked automatically on GitHub: required fields present, date format valid, source URL present.

### Collection and verification

1. **Collect**: an AI drafts events with web search, each with a source URL.
2. **Verify**: an AI in a separate conversation opens the source and checks the date and content; if they match, the event is marked **verified**. Events added by pull request also start as unverified.
3. **Use**: only verified events are used in outputs. Human review catches anything that feels off when reading the timeline at stage 3.

Event descriptions are written in our own words, and sources are kept as URLs. Tracking parameters at the end of URLs (such as utm_source) are removed.

## 8. Era correctness checks

Checks run in two layers. The reference point is not the public release date but the tool versions the company actually uses.

1. **Machine**: a script looks for things that were not yet available at the time (for example, C# async from 2012, VB.NET string interpolation from 2015). Detection patterns are attached to technology events in the timeline, so the timeline database itself serves as the yardstick.
2. **AI**: the Inspector looks for "modern feel" that machines miss, such as naming or class design that is too contemporary.

Even in 2013, a company still on Visual Studio 2008 cannot use async.

## 9. Human checkpoints and screen

The lab stops for human review at stages 1, 3, and 5, and runs stages 2 and 4 without stopping.

| Checkpoint | What to review |
| --- | --- |
| Stage 1: conditions set | The conditions entered |
| Stage 3: timeline ready | A human-readable timeline (one or two pages) exported from SQLite by a script |
| Stage 5: before packaging | The Inspector's report and build results; decide whether to release |

- Humans do not read all the code from stage 4. Skim the decision notes if something looks off.
- Screen: a Python script writes a single HTML file to open in a browser. Nothing extra to install. Stages 1 to 5 are shown as tabs.
- The first-release screen is view-only. Approvals and change requests go through conversation with the agent.

## 10. Open-source policy

| Item | License |
| --- | --- |
| The kit (scripts and instructions) | MIT |
| Timeline data (including pull requests) | CC BY 4.0 |
| Outputs users create with the kit | The user's choice |

### Boundary with the real world

- Companies, people, and business partners are all fictional. Product names of the era (Windows, SQL Server, Crystal Reports) are used as they are.
- No real project is used as a model. Experience is used to judge authenticity; subjects are built from fiction.

### Marking outputs as reproductions

- Each output's README begins by stating that it reproduces a fictional company's system and does not exist.
- A single file at the top level summarizes the conditions and how the output was made.
- Commit dates are never backdated.
- No notices are added to individual code files, to keep them natural as code from their era.

### Discoverability

- The repository description, topics, and the opening of the README are written in English, using the words people are likely to search for (synthetic legacy code, modernization benchmark, .NET Framework, VB.NET, and similar).
- A citation file (CITATION.cff) is included from the start.
- Whether to assign a DOI to releases (via Zenodo) is decided at the first release.

## 11. Non-functional requirements

Three rules for the lab: keep setup light, do not overuse tokens, automate what can be automated.

- Time, AI usage, and size limits per system are decided after measuring the first run.
- For each system the lab records: time spent (per stage), AI usage (which stage, which role, how much), and size (number of screens, lines of code, number of phases).
- Scripts run on Windows, macOS, and Linux.
- The standard build check runs on a GitHub Actions Windows runner, so Mac users can check builds without owning Windows. Local Windows builds are optional.

## 12. First-release completion criteria

The first release is complete when all three hold:

1. **Yardstick system**: running stages 1 to 5 with the same conditions as AI-LMC produces a world and timeline that the maintainer judges comparable to AI-LMC's hand-made materials.
2. **A second system with different conditions**: with a different industry or era (within Japan × .NET), stages 1 to 5 complete and the build passes on GitHub Actions.
3. **README alone is enough**: a fresh AI agent session given only the README builds one system without getting stuck.

## 13. Related work and differences

As of 2026-10-30, we found no general-purpose kit that reproduces legacy systems from varying conditions with an era boundary and provenance.

| Related work | What it does | Difference |
| --- | --- | --- |
| [Spec2COBOLRot](https://arxiv.org/pdf/2609.26835) (AISM '26) | Generates COBOL from specifications and degrades it with patterns from production code, to build modernization benchmarks | No timeline or era boundary. Driving a legacy-developer agent through a multi-decade maintenance plan is listed as future work |
| [eShopModernizing](https://github.com/dotnet-architecture/eShopModernizing/wiki) | Fictional eShop back-office apps (WebForms, MVC, WinForms + WCF) | Fixed samples, simple CRUD, no provenance |
| [EnterpriseModernizationSample](https://github.com/jongalloway/EnterpriseModernizationSample) | A fictional pizza franchise's .NET Framework business system, before and after migration | A single fixed system, not a kit for varying conditions, no provenance |
| GitChameleon 2.0 and similar research | Benchmarks for version-conditioned code generation | Not a kit for building subjects. Even leading models succeed only about half the time, which supports the machine check in section 8 |

legacy-lab differs in three ways: it builds any number of systems from varying conditions; it enforces an era boundary by work date; and it delivers code and provenance together.

## 14. To be decided in basic design

- Timeline field definitions (source, verification status, affected countries and industries, era-check patterns; the two dates are already decided)
- How company events are created at stage 3
- What the Inspector may see
- Repository structure
