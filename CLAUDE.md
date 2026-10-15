# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A study/interview-prep knowledge base about **modernizing a legacy Delphi system to modern .NET**,
and how architecture concepts (Clean Architecture, DDD, Modular Monolith, Microservices, Messaging,
data migration, testing) support that process. It is Markdown only — there is no code, build, lint
or test tooling.

## Structure and conventions

- `docs/NN-topic.md` — numbered documents. `README.md` holds the index table and suggested reading
  order; **when adding, renaming or removing a doc, update the README table** (and the cheat sheet if
  the topic is core).
- `docs/00-cheat-sheet.md` is a one-page condensation of all other docs — keep it in sync when a key
  idea changes elsewhere.
- `docs/11-interview-questions.md` holds Q&A with model answers; it cross-references concepts
  explained in docs 01–10 rather than re-explaining them.
- Docs cross-reference each other as "doc NN" (e.g. "see doc 07"). Renumbering a file means fixing
  those references too (`grep -rn "doc [0-9]" docs/`).
- Every architecture doc ends with (or contains) a section on **how the concept helps a Delphi → .NET
  migration**, plus pitfalls and an "interview one-liner" where relevant — follow that shape for new
  topic docs.
- Diagrams are Mermaid code blocks (rendered by GitHub); code samples are C# targeting .NET 10.
- Content is written in English.

## Time-sensitive facts

Several statements depend on the date (written Oct 2026) and should be re-verified when edited:
.NET support dates (.NET 10 LTS until Nov 2028, .NET 8 ends Nov 2026, .NET 11 STS), library licensing
(MediatR, AutoMapper, MassTransit v9 commercial), and "Aspire" branding. These appear mainly in
`docs/10-modern-dotnet-stack.md`, `docs/07-messaging.md` and `docs/11-interview-questions.md`.
