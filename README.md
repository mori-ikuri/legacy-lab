# legacy-lab

**Reproduce legacy business systems with AI agents: era-bounded, with the timeline and decisions behind the code.**

legacy-lab is a kit for building synthetic legacy codebases to test migration tools and AI-driven modernization. You give it conditions (language, era, industry, country, scale). It builds a fictional company, a timeline of the business events that shaped its system, and the code itself, written phase by phase by a developer agent that only knows what was known on its work date.

Each output comes as a pair: **code + provenance** (the event timeline and the developer's decision notes). With the provenance, you can check whether a migration preserved the business decisions, not just the syntax.

The first release targets Japanese business systems on .NET Framework (VB.NET and C#, 2002 onward).

> **Status:** Requirements approved (2026-10-30). Basic design is next. Nothing runnable yet.

## Documents

- [Requirements](REQUIREMENTS.md) ([日本語](REQUIREMENTS.ja.md))

## Background

legacy-lab generalizes [AI-LMC](https://github.com/mori-ikuri/ai-lmc), a hand-built corpus in which an AI developer, "Yamada," maintained a fictional company's VB.NET system from 2008 to 2017, one business event at a time.

## License

- Kit (scripts and instructions): MIT
- Timeline data: CC BY 4.0 (added together with the data)

---

## 日本語

legacy-lab は、言語・年代・業種・国・規模を条件として入れると、その時代らしいレガシー業務システムを「コード+成り立ち(出来事の年表と判断メモ)」で再現する仕組みです。移行ツールやAIによるモダナイゼーションを試す人のための題材づくりを目的にしています。

いまは要件定義が確定したところで、次は基本設計です。詳しくは[要件定義](REQUIREMENTS.ja.md)を見てください。
