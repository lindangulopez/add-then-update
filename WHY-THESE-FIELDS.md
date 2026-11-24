# Why these ten fields

The survey and the card ask about ten CodeMeta fields. This note explains where they come from, using four references:

| Reference | What it is | What it gives the session |
|---|---|---|
| [RSMD guidelines](https://fair-impact.github.io/RSMD-guidelines/) (FAIR-IMPACT, v1.0, 2024) | Guidance for software creators and curators, with a checklist of questions in seven aspects | **What good metadata should cover** |
| [CodeMeta's own `codemeta.json`](https://github.com/codemeta/codemeta/blob/master/codemeta.json) | The CodeMeta project described with its own vocabulary (CodeMeta 4.0) | **The terms, and how to write them** |
| [Prepare your metadata](https://docs.softwareheritage.org/user/deposit/howto/prepare.html) (Software Heritage) | How to build the Atom XML file sent with a deposit | **Which fields the deposit requires or recommends** |
| [The metadata-only deposit](https://docs.softwareheritage.org/devel/swh-deposit/specs/spec-meta-deposit.html) (Software Heritage) | The spec for sending metadata without code, about something already in the archive | **How to update without sending new code** |

## The five core fields: what the deposit asks for

The Software Heritage "prepare" page lists two **required** fields, `codemeta:name` and `codemeta:author`, and three **recommended** fields, `codemeta:version`, `codemeta:description` and `codemeta:license`. Those five are the core fields of the sheet.

The RSMD checklist asks for the same five: name, description and version among the descriptive metadata (RSMD 4.2), authors in the code and in the record (RSMD 5.1, 5.2) with people identifiers such as ORCID (RSMD 5.3), and the licence in the code and in the record (RSMD 6.2, 6.3).

`version` does a second job in a deposit: it is how Software Heritage tells apart several deposits of the same origin. A new version needs a new `codemeta:version`; a deposit with the same value replaces the earlier one in the next snapshot, though the earlier one stays in the archive under its SWHID.

## The five others: what RSMD adds

Each of the other five answers an RSMD checklist question that the required fields leave open:

| Field | RSMD question |
|---|---|
| `identifier` | Can the project and its releases be identified with extrinsic identifiers? (RSMD 3.3) |
| `softwareRequirements` | Are the dependencies described, in a machine-actionable format? (RSMD 7.1) |
| `operatingSystem` | Are the operating system and environment requirements described? (RSMD 7.2) |
| `codeRepository` | If the code is in an online version control system, is its URL in a code repository property? (RSMD 1.3) |
| `referencePublication` | Can articles describing the software be reached through a persistent identifier? (RSMD 4.3) |

They were picked because a repository often holds this information (a DOI, a linked article, a Git URL) without sending it with the code. That is the "held, not sent" column on the sheet.

## Left out, on purpose

RSMD 4.2 also lists domain, programming language, date created, date of first publication, keywords and related links, and RSMD 5 covers author roles and a citation preference. All have CodeMeta terms, and several appear in the Software Heritage examples. They were left out only to keep the exercise to ten rows in twenty minutes. **The ten fields are a choice for this session, not a standard** from RSMD, CodeMeta or Software Heritage.

## Two differences to know about

- **CodeMeta version.** CodeMeta's own `codemeta.json` uses CodeMeta 4.0. The Software Heritage deposit accepts CodeMeta v2 for now (the "prepare" page says v3 will follow). All ten terms exist in v2, so the sheet works with either.
- **How the licence is written.** In a `codemeta.json` file, the licence is usually its SPDX URL (CodeMeta's own file uses `https://spdx.org/licenses/Apache-2.0`). The deposit examples write it as `<codemeta:license><codemeta:name>…</codemeta:name></codemeta:license>`. The card follows the `codemeta.json` form, which RSMD 6.6 also points to (an SPDX licence identifier).

## Adding, then updating

The Software Heritage deposit has three kinds, set in the `<swh:deposit>` element:

1. **Initial deposit** (`swh:create_origin`): the first code deposit for an origin.
2. **New version** (`swh:add_to_origin`): new code for the same origin, with a new `codemeta:version`.
3. **Metadata-only deposit** (`swh:reference`): no code, one Atom XML file about an origin or a SWHID already in the archive. The spec says the metadata is "injected as a metadata entry of the relevant object, with the information about the contributor of the deposit". It allows SWHIDs of origins, snapshots, releases, revisions, directories and contents, with the `origin`, `visit`, `anchor` and `path` qualifiers, but not `lines`.

Steps 3 and 4 of the session follow this order: first map what goes in with the code, then decide how a change reaches the archive.
