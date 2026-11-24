# Timeline format

English | [日本語](timeline-format.ja.md)

> The Japanese version is authoritative. If the two versions differ, follow the Japanese version.

How to write rows in the timeline (`data/events/`). It is written so that anyone adding or fixing timeline rows by pull request needs to read only this document. The design of the kit is in [design.md](design.md), and the requirements are in [Requirements](../REQUIREMENTS.md). Stage 1 to stage 5 refer to the five stages in the requirements (stage 1 Conditions, stage 2 World, stage 3 Timeline, stage 4 Implementation, stage 5 Packaging). A date in parentheses is the date the rule was decided.

The timeline holds one event per line of JSON, and its fields are divided into six groups (decided 2026-10-30).

## Location

- Rows go in `data/events/<timeline>/<year>.jsonl`. There are two kinds of timeline: one per country (two letters, such as `jp`), and `world`, which holds events common to the whole world (such as versions of .NET and Windows).
- There are no regional timelines, such as one for the EU. An event decided by a region is added to the timeline of each country (this is also more accurate, because the date an EU directive becomes national law differs from country to country).
- A company reads the timeline of its own country and the `world` timeline. The first release builds two: `jp` and `world`.
- title and impact are written in the local language of each timeline (Japanese for `jp`). `world` is in English.
- The year is the year of the known date, so that filtering by work date never has to open files from the future. The pull request check confirms that the year of the file matches the year of the known date.

## Fields

| Group | Field | Required | Content |
| --- | --- | --- | --- |
| Identity | id | Required | Country-year-short name (e.g. jp-2012-ctax8-law). Not a serial number |
| Identity | thread | Only for stories with two or more stage rows | A name that links the stages of the same story (e.g. jp-ctax8) |
| When | known | Required | Known date. One of YYYY, YYYY-MM, or YYYY-MM-DD |
| When | occurred | Required except for cancelled | Occurred date. For stages other than effective, the planned date as of that point. Empty if not yet decided (also empty for cancelled) |
| What | kind | Required | Kind (law, standard, tech, society) |
| What | stage | Required | Stage (decided, confirmed, changed, effective, cancelled) |
| What | title | Required | Headline |
| What | impact | Required | How it affected business systems (one line). Events that cannot be written in one line are not collected |
| Scope | industries | Required | Industries. ISIC Rev.4 section letters; `*` for all industries |
| Scope | areas | Required | Business areas. One or more of the lab's 10 terms |
| Reliability | sources | Required | Source URLs (one or more) |
| Reliability | status | Required | unverified or verified |
| Reliability | verified | Required when verified | The date it was verified (on) and by whom (by) |
| Era check | tech | Required when kind is tech | The tool (product), its version (version), and how to detect it (markers) |

The country is determined by the location, so it is not a field.

An "empty" occurred is stored as JSON `null`, and the key is kept (e.g. `"occurred":null`) (decided 2026-11-11).

## Sample row

```json
{"id":"jp-2012-ctax8-law","thread":"jp-ctax8","known":"2012-08","occurred":"2014-04-01","kind":"law","stage":"decided","title":"消費税率8%への引き上げが法律で決まる(実施は2013年秋に最終判断)","impact":"税率を固定値で持つ販売・会計・請求の仕組みは、施行日で税率を切り替える改修が要る","industries":["*"],"areas":["sales","accounting","billing"],"sources":["https://..."],"status":"unverified"}
```

## Known date

The known date is the date when the basis on which the Developer could start responding became public.

| Kind | Known date | Occurred date |
| --- | --- | --- |
| law | For a law, the date it was enacted. For cabinet orders, ministerial ordinances, and public notices, the date they were promulgated | The date it came into force |
| standard | The date it was announced | The date it began to apply |
| tech | The date its release or provision began | The same date (for end of support, the end date) |
| society | The date it happened | The same date |

- When only the year and month, or only the year, is known, the index treats the event as known on the last day of that period. This errs on the side of keeping the future out.
- Do not include news reports or rumors from the consideration stage. Rumors are handled on the company-event side ([Design](design.md), chapter 2).
- End of support is held as a decided row whose known is the date the planned end date became public, and an effective row on the end date. For products whose planned end date is set at release, such as Microsoft products, known is the release date. If support is extended, add a changed row.
- When an end date was set by a policy announcement that names neither the product nor the end date, known is the date the policy became public, because that is the date the basis on which the Developer could calculate the end date became public. Use two sources: the policy announcement and the product's lifecycle page (decided 2026-10-31. Example: the 2004-05-25 policy that extended Extended Support to five years set the end dates of products from 2003).
- One row is one event. The event written in title and the known date refer to the same event, and the source states that date (decided 2026-10-31). Do not settle on a single correct release date for each product. Even for the same product and version, different events (availability for businesses, general retail release, becoming usable on another OS, and so on) go in separate rows.
- In title, use the wording the source uses for the event (launched, released to manufacturing (RTM), generally available, and so on). Do not use wording that does not say which event it is, such as "ships" (decided 2026-10-31).
- For a row whose only source is Microsoft's official lifecycle page, title says the product "is released" (「発売される」), and the date is the "Start Date" on that page (decided 2026-10-30; on 10-31 this was repositioned as the way to pick the date within the rule above). This is because it can be verified on a single official page.
- If a source confirms that the same product and version became available earlier than the existing "is released" row (availability for businesses, availability through volume licensing, general availability, and so on), add it as a separate effective row. This is because a company can use a product from the earliest availability whose conditions it meets ([Design](design.md), chapter 2). Do not add an availability on the same date as an existing row (decided 2026-11-11).
- An effective row for the start of a product's use (its release or the start of its availability) is added only after a source published on or after that day confirms that it started that day. An advance notice alone (such as "availability will start on day X") is not enough. Add the row once a source from after the fact is found (decided 2026-11-11).
- When the same version has two or more event rows, put markers on only one of them, so that the check does not pick up the same thing twice.

## Stages and thread

A story that goes through stages gets one row per stage, linked by the same thread.

| stage | Meaning | Date rule |
| --- | --- | --- |
| decided | Decided | The occurred date is after the known date. Empty (undecided) if the date is not yet set |
| confirmed | The implementation or the date was fixed by a law, cabinet order, or the like | The occurred date is after the known date |
| changed | Changed (postponed, brought forward, or changed in content) | The occurred date is after the known date. Empty (undecided) if the date is not yet set |
| effective | Implemented or happened | The known date and the occurred date are the same |
| cancelled | Called off | The occurred date is empty |

- The plan as of the work date is the occurred date of the row in the same thread whose known date is the latest on or before the work date. If that row is cancelled, there is no valid plan. If it is not cancelled and its occurred date is empty, read the planned date as undecided. For a single-row event without a thread, read that row (added 2026-11-11, when chapter 2 of [Design](design.md) was decided).
- Stage rows are only appended; earlier rows are not rewritten. Rewriting to correct a mistake is fine.
- When the date something was effectively decided differs from the date its date was formally fixed, split the rows. The point at which it was effectively decided (a law being enacted, a formal statement by the Prime Minister, and so on) becomes a decided or changed row, and the point at which the date was fixed by a law or cabinet order becomes a confirmed row. The known date of each row is the date of its own point (decided 2026-11-11).
- An event that is complete in one row, such as an earthquake disaster or a product release, has only one effective row and no thread.

Example: the consumption tax at 10% (dates are provisional; the sources are checked during draft collection)

| Stage | Known date | Occurred date | File |
| --- | --- | --- | --- |
| decided (decided by law) | 2012-08 | 2015-10-01 | jp/2012 |
| changed (first postponement) | 2014-11 | 2017-04-01 | jp/2014 |
| confirmed (law for the first postponement enacted) | 2015-03 | 2017-04-01 | jp/2015 |
| changed (second postponement) | 2016-06 | 2019-10-01 | jp/2016 |
| confirmed (law for the second postponement enacted) | 2016-11 | 2019-10-01 | jp/2016 |
| effective (implemented) | 2019-10-01 | 2019-10-01 | jp/2019 |

## Kinds, industries, and business areas

| kind | Content | Examples |
| --- | --- | --- |
| law | Laws and public systems (including taxes, era name changes, and public fee systems) | Consumption tax, era name change, My Number |
| standard | Industry standards and agreements | Zengin format, character encoding standards |
| tech | Products and technologies | .NET versions, end of Windows support |
| society | Society, economy, and disasters | Earthquake disasters, the Lehman Shock |

- Industries are ISIC (International Standard Industrial Classification) Rev.4 section letters (A to U). To divide more finely, add lower-level digits (e.g. C10). Do not mix in Rev.5, because the meaning of the codes from J onward changes. When moving to Rev.5, reassign everything at once using the UN correspondence table (decided 2026-11-11).
- Business areas are these 10 terms: accounting, sales, billing, purchasing, inventory, production, logistics, payroll, hr (human resources and attendance), platform (infrastructure: OS, DB, character encoding, and so on).

## Reliability

- Each event has one or more sources. Remove tracking parameters at the end (such as utm_source). For a page that has disappeared, a Web Archive (web.archive.org) URL is fine.
- When marking a row verified, record the date and who in verified. Example: `"verified":{"on":"2026-11-02","by":"claude-code-cloud"}`
- A row stops being verified only when its fact fields (known, occurred, stage, sources, and tech's product and version) change. title, impact, industries, and areas are wording and judgment fields, so fixing them does not remove verified. These are what a human looks at when reading the timeline at stage 3. The two tech fields were added because chapter 2 of [Design](design.md) made them the key to "from when that version could be used" (decided 2026-11-11).
- A row that the verifier fixed to match the source they opened may be marked verified on the spot. A fix that needs a new source, or that splits or adds rows, goes back to unverified.
- Verification starts with the rows that will be used. When one system is built, a script lists the unverified rows that fall under the stage 1 conditions (country, era, industry) and hands them to the verifier together.
- Whether a row was verified by a separate conversation cannot be fully checked by machine, so it is enforced by the written procedures.

## Detection for the era check

Rows whose kind is tech get `tech`.

```json
"tech":{"product":"csharp","version":"5.0","markers":[{"lang":"cs","name":"async/await","pattern":"\\basync\\s+(Task|void)\\b"}]}
```

- The check script collects tech events newer than the tool versions the company uses at that point, and applies their markers to the code. The reference point is the company's tool versions, not the date.
- The machine only goes as far as picking up suspicious spots. The Inspector makes the judgment (because regular expressions also match inside comments and strings).
- For a feature that arrived in VB.NET and C# at the same time, list markers with different lang values in one event. Besides cs and vb, lang can also be sln (solution files) and proj (project files). Events that do not appear in code (such as end of support) get no markers.
- In the first release, product is limited to eight: csharp, vbnet, dotnet-framework, visualstudio, windows, sqlserver, office, crystalreports. Others are added by pull request (visualstudio, office, and crystalreports were added on 2026-10-30).

## License of rows sent by pull request

Rows contributed to `data/` are deemed to be submitted under CC BY 4.0, and contributions to the kit under MIT ([data/LICENSE](../data/LICENSE), [LICENSE](../LICENSE)).
