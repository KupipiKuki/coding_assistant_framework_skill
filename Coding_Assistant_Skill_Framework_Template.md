# Coding Assistant Skill Framework Template

## Purpose

This framework turns a working, program-specific coding assistant into a reusable skill that produces correct, reviewable work against a defined version of a platform. It treats platform knowledge as maintained evidence, not model memory.

A skill built with this framework should help an assistant:

- Identify the user’s actual platform, version, runtime, and task context.
- Find authoritative current API and format evidence before proposing code.
- Use verified examples and domain rules where the API documentation is incomplete.
- Generate code that fits the target runtime and workflow.
- Validate claims at the level the available environment supports.
- State assumptions, unknowns, and compatibility limits plainly.
- Update efficiently when the platform changes.

This is a framework and copyable template. Replace every bracketed placeholder before publishing a skill.

## Design rules

1. **Evidence before recall.** Use searchable, versioned reference material for API names, arguments, return behavior, schemas, and domain rules. Do not rely on model memory for platform-specific details.
2. **Currentness is explicit.** Record the supported version range, source release/version, source-check date, and known compatibility boundaries.
3. **Examples prove patterns, not universal rules.** Label what each example demonstrates and whether it was executed against the target application.
4. **Unknown stays unknown.** When evidence is missing or conflicting, search primary sources, test safely if possible, and mark the unresolved point. Never fill gaps with plausible API names.
5. **Validation claims match the evidence.** Static review, unit tests, sandbox execution, and target-application import/run are different levels. Report the level actually completed.
6. **Keep user work reviewable.** Prefer minimal, explainable changes; preserve the user’s conventions and data; identify generated or changed files.
7. **Minimize repeated investigation.** Index knowledge, expose practical search tools, cross-reference related concepts, and save lessons from verified failures.
8. **Separate layers.** Distinguish API methods, object fields, configuration, persisted formats, database fields, and UI labels. Similar names do not imply interchangeable behavior.

## Framework inputs

Before drafting a skill, gather and record:

| Input | Required information |
| --- | --- |
| Target platform | Product, module, edition, runtime, host environment |
| Version scope | Exact tested versions and broader intended support range |
| User tasks | Common requests the skill should support and tasks out of scope |
| Primary sources | Official documentation, release notes, schemas, source code, vendor examples |
| Local evidence | Working scripts, exports, fixtures, configuration, databases, error logs |
| Validation access | Whether the target runtime/application is available and how results can be verified |
| Distribution constraints | Skill host format, file limits, supported tools, offline or network requirements |
| Risk boundaries | Operations that can modify production data, trigger external effects, or require explicit user review |

If an input cannot be obtained, document the gap and its effect on confidence. Do useful work that does not depend on the missing information; do not imply compatibility that was not checked.

## Evidence model

Use a consistent evidence label in reference catalogs and implementation notes:

- **Official-current:** official docs, schemas, or release notes for the supported version; source version/date recorded.
- **Official-versioned:** official material for a named older or otherwise specific version.
- **Observed-working:** executed successfully in a named target version or environment; include a reproducible context when possible.
- **Observed-export:** captured from a real application export or runtime inspection; proves representation in that example, not necessarily all valid cases.
- **Example-only:** supplied example or tutorial not independently executed.
- **Inferred:** derived from multiple evidence items; explain the reasoning and confidence.
- **Unverified:** plausible lead only; do not present as established behavior.

When sources conflict, prefer the source that applies to the exact target version and task. Record the conflict and chosen interpretation. Preserve source links, titles, versions, access dates, and relevant section or record paths so claims can be rechecked.

## Development workflow and stop gates

### Phase 0 — Define the contract

Write a one-page scope statement with target users, supported tasks, platform/version matrix, inputs/outputs, and explicit exclusions.

**Stop gate:** Do not call a skill “current” or “compatible” until the version scope and evidence-check date are stated. If the exact version is unknown, design a version-detection question or a safe compatibility branch.

### Phase 1 — Build the source inventory

Collect official docs, release notes, API references, schemas, SDK/package references, and authoritative examples. Inspect local working projects and exports when provided. Create an inventory containing:

- Source title and publisher
- URL or local path
- Applicable platform/version
- Date accessed or captured
- Evidence type
- Topics and API areas covered
- Known omissions or caveats

Use primary sources for technical behavior. Search secondary sources only to discover leads or known issues, then confirm important claims against primary evidence or a reproducible target test.

**Stop gate:** Every API surface used by a sample must have a traceable source or an explicit “observed-working” record. Uncovered areas are marked as gaps.

### Phase 2 — Normalize the knowledge base

Organize the source material so the assistant can retrieve exact information efficiently. Choose formats that preserve structure and provenance:

- API catalog: exact name, signature, receiver/type, arguments, return values, errors, version range, source path.
- Data/schema catalog: field name, type, constraints, relationships, defaults, version range, provenance.
- Example catalog: task, full source, dependencies, expected context, target version, execution status.
- Cross-reference catalog: where a function/field/type appears and related concepts.
- Troubleshooting catalog: symptom, cause, diagnostic evidence, safe fix, affected versions.
- Change log: added, changed, deprecated, removed, and unverified items by release.

Keep source text or excerpts where legally and operationally appropriate; otherwise record concise paraphrases with precise citations/paths. Never flatten distinct namespaces into one ambiguous list.

**Stop gate:** A reviewer can search for a named API and inspect the full signature, version, provenance, and a relevant example without reading the whole corpus.

### Phase 3 — Implement retrieval and guardrails

Provide small, predictable search commands or an equivalent retrieval interface. It should support:

- Exact lookup by symbol/name
- Search by concept or task
- Filtering by artifact type, version, and evidence level
- Full detail for a selected record
- Source/path output for every result
- Structured output for automation when useful

Document commands and sample results. Ensure the skill can be used from an arbitrary working directory if that is expected. Treat paths and filenames as data; avoid commands that can modify user projects during reference search.

**Stop gate:** Try representative lookups for functions, fields, examples, formats, and a known edge case. Each should return the intended source and enough context to avoid guessing.

### Phase 4 — Draft the skill instructions

Write concise operational instructions that tell the assistant when to use the skill, how to search evidence, how to handle uncertainty, how to generate code, how to validate, and how to report sources and limitations. Keep long catalogs and examples in bundled reference files rather than overloading the main instructions.

Include:

- Skill name and a task-specific description
- Supported platform/version scope and currentness metadata
- Required evidence lookup before nontrivial code
- Namespace distinctions and naming constraints
- Runtime, selection, object, and project assumptions
- Safe edit and data-preservation behavior
- Validation procedure and truthful result wording
- Escalation behavior when the target version or evidence is missing
- Known caveats and unsupported tasks

**Stop gate:** A new user request should lead the assistant to gather the minimum necessary version/context, search exact evidence, generate a scoped result, and report what was verified.

### Phase 5 — Validate knowledge and behavior

Validate at several separate levels. Mark each result independently:

1. **Package validation:** required files, metadata, links, parsable JSON, script syntax.
2. **Retrieval validation:** exact and concept searches return correct records and provenance.
3. **Generation validation:** representative tasks produce code using evidenced signatures and correct object/field distinctions.
4. **Static validation:** syntax/lint/type/schema checks appropriate to the language and formats.
5. **Isolated execution:** run against a sandbox, mock, or non-production fixture where available.
6. **Target validation:** import, execute, or inspect output in the supported application/runtime.
7. **Regression validation:** replay past failures and version-sensitive cases.

Capture test inputs, version/environment, commands or steps, expected result, actual result, and evidence artifact. Do not describe a generation review as a target-application test.

**Stop gate:** Core tasks pass at the highest available validation level. Any missing target runtime is a visible limitation in the release metadata and skill instructions.

### Phase 6 — Package and release

Package the skill in the platform’s required structure. Verify metadata, references, scripts, dependencies, examples, and relative paths from a clean location. Keep a release manifest:

- Skill version and release date
- Supported target versions
- Documentation/source check date
- Tested target versions and validation levels
- Files and reference dataset versions
- Known issues, unsupported behavior, and migration notes
- Reviewer or validation record where applicable

**Stop gate:** Install or load the packaged skill in its intended host and perform at least one end-to-end representative task.

### Phase 7 — Maintain for platform change

Use platform release notes, deprecation notices, SDK changes, schema changes, and verified user-reported failures as update triggers. For each trigger:

1. Identify affected API/data records and dependent examples.
2. Confirm the change from primary sources for the target version.
3. Update catalogs, retrieval indexes, examples, and compatibility rules.
4. Re-run impacted retrieval and regression cases.
5. Update version metadata and change log.
6. Publish a new skill version with compatibility notes.

Do not claim continuous monitoring unless an actual scheduled process exists. If updates are manual, state that source currency is checked as of the recorded date.

## Copyable skill file template

Use this as the starting point for the main `SKILL.md`. Keep it short enough to act as operating instructions; put detailed evidence in references.

```markdown
---
name: [lowercase-skill-name]
description: [What the skill does, for which platform/version, and when to use it.]
---

# [Skill display name]

## Supported platform and version

- Product/module: [name]
- Supported versions: [exact range; distinguish tested from intended]
- Runtime/environment: [language, host, operating system, deployment context]
- Source checked: [date]
- Skill/reference release: [version]
- Validation: [highest completed level and exact tested versions]

If the user’s target version is outside this range or unknown, establish it before using version-sensitive APIs. Do not claim compatibility beyond the evidence recorded in `references/[manifest-file]`.

## Use the references first

Before writing or changing nontrivial code:

1. Search for the exact API/type/field and the user’s task pattern.
2. Open the full record, including signature, version, return behavior, constraints, and source path.
3. Inspect at least one relevant example and its tested status.
4. Check cross-references, deprecations, and known issues.
5. If evidence conflicts or is missing, investigate the supported primary sources or ask for the missing runtime/version context. Keep unresolved behavior explicit.

Search interface:

```text
[command] [query] --kind [api|field|example|schema|format|issue] [--version VERSION] [--detail]
```

Always report the evidence files/record paths used. Never invent API names, arguments, return values, defaults, or format rules.

## Coding rules

- Preserve exact spelling, receiver/type, argument order, return behavior, and version scope from the catalog.
- Keep [API methods], [object/data fields], [configuration], and [serialized/export fields] distinct.
- Follow the target runtime’s required context, lifecycle, permissions, and error handling.
- Preserve existing project conventions and unrelated data.
- Make assumptions visible, especially [domain-specific sizing, units, identity links, defaults, or destructive behavior].
- Prefer the smallest change that satisfies the request.

## Validation and response

Validate using the strongest available applicable level: [package / retrieval / static / isolated / target / regression]. State the exact level and environment. If the target application was unavailable, say that the result is not target-validated.

For generated or edited work, summarize:
- What changed and where
- Key evidence sources and catalog paths
- Validation performed and outcome
- Remaining assumptions, limitations, and follow-up checks

## Known limitations

- [Known version-specific behavior]
- [Unsupported tasks or data]
- [Validation gaps]
```

## Copyable evidence record templates

### API or behavior record

```yaml
id: [stable-record-id]
kind: api | field | schema | behavior | format
name: [exact name]
namespace_or_type: [receiver/type/module]
signature_or_shape: [exact signature or schema]
arguments: [names, types, required/optional, constraints]
returns_or_effects: [documented behavior]
errors_or_edge_cases: [known conditions]
version_range: [from/to or unknown]
evidence_level: Official-current | Official-versioned | Observed-working | Observed-export | Example-only | Inferred | Unverified
source:
  title: [source title]
  url_or_path: [URL or local path]
  section_or_record: [anchor, page, JSON path, line, etc.]
  accessed_or_captured: [YYYY-MM-DD]
tested:
  target_version: [exact version or null]
  environment: [runtime/environment or null]
  result: [pass/fail/not-run]
notes: [scope limits and conflicts]
```

### Example record

```yaml
id: [stable-example-id]
task: [what it demonstrates]
file: [path]
language_or_format: [name]
platform_version: [exact version or unknown]
dependencies: [required libraries, equipment data, configuration, model context]
evidence_level: [label]
execution_status: [executed-pass | executed-fail | inspected-only | example-only]
expected_result: [specific observable outcome]
known_limits: [what the example does not establish]
source: [provenance]
```

## Release audit checklist

Before publishing a skill or version update, verify:

- [ ] Skill metadata describes actual supported tasks and platform scope.
- [ ] Tested versions are separated from intended but untested versions.
- [ ] Source check date and release/version metadata are present.
- [ ] Every used API and format rule has provenance and a version label.
- [ ] Current official docs and relevant release notes were checked.
- [ ] Namespaces, fields, methods, schemas, and UI labels remain distinct.
- [ ] Examples identify dependencies and execution status.
- [ ] Retrieval supports exact lookup and full-record inspection.
- [ ] Search paths and references work from a clean install.
- [ ] Core tasks and prior failure cases were replayed.
- [ ] Validation claims match captured evidence.
- [ ] Known gaps, unsupported cases, and migration notes are visible.
- [ ] The packaged skill completed an end-to-end task in its intended host.

## Applying the framework to the WindMil reference skill

The WindMil skill already demonstrates several strong framework patterns: bundled API and field catalogs, a purpose-built lookup script, cross-reference data, export-format references, and working model examples. Its task instructions also preserve domain rules such as switch pairing and transformer/consumer parent relationships, while explicitly disclosing cases that remain unimported or require local EQDB verification.

For a new versioned release, use this framework to add or confirm a compatibility manifest, source check date, exact WindMil version coverage, and validation records for each key script/model behavior. Preserve the current distinction between catalog evidence, observed export representation, and successful import into WindMil. This makes the skill reusable without overstating what has been tested.
