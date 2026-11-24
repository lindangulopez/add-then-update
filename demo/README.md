# Demo: three software records from one institutional repository

A worked example of the survey on three real software records from a DSpace institutional repository that plans to send software metadata to Software Heritage through the deposit. The records are **anonymised** (A, B, C): no repository, record, author or link is named. The point is the method and the patterns, not the records.

## The question

Before a repository sends a metadata-only deposit, which of the ten CodeMeta fields can it actually send today, and where does the rest get lost?

## The logic

### Four statuses

| Status | Meaning |
|---|---|
| **Sent to deposit** | Already sent to Software Heritage in a deposit. None yet for this repository |
| **Can be sent** | In the repository's machine export, so a deposit client could map it to CodeMeta today |
| **Held, not sent** | The information exists (on the record page, on Zenodo, in the code) but not in that export |
| **Not held** | Nowhere |

### What "the machine export" means

A record page shows what people see. A deposit client reads what machines get. Here that is the **DataCite export over OAI-PMH**, which DSpace serves for every record:

```text
https://<repository>/dspace-oai/request?verb=GetRecord&metadataPrefix=datacite&identifier=oai:<repository>:<handle>
```

A field counts as "can be sent" only if it is in that export. A field on the page but not in the export is "held, not sent".

### Four layers compared, for each record

1. **The record page** (what a person sees)
2. **The machine export** (what a deposit client would read)
3. **Upstream**: the Zenodo record the repository copied, and the code repository it came from (`CITATION.cff`, `pyproject.toml`, `LICENSE`, `codemeta.json` if any)
4. **The archive**: is the code already in Software Heritage? A metadata-only deposit must point to an origin or a SWHID that is already there. Checked with the public API: `https://archive.softwareheritage.org/api/1/origin/<origin URL>/visits/`

### Rules used

- Software Heritage, [Prepare your metadata](https://docs.softwareheritage.org/user/deposit/howto/prepare.html) (required and recommended fields) and [The metadata-only deposit](https://docs.softwareheritage.org/devel/swh-deposit/specs/spec-meta-deposit.html)
- The [RSMD guidelines](https://fair-impact.github.io/RSMD-guidelines/) checklist (FAIR-IMPACT)
- The [RsMetaCheck catalogue](https://github.com/SoftwareUnderstanding/rsmetacheck/blob/main/docs/catalog.md) of pitfalls (P) and warnings (W), for the code side

Why these ten fields: [WHY-THESE-FIELDS.md](../WHY-THESE-FIELDS.md).

## The three records

All three are copies of Zenodo records, so the repository's rule ("a Zenodo DOI means a copy, so send metadata only") puts all three in the metadata-only group.

- **Record A**: a Python package, released on GitHub and archived on Zenodo with its GitHub link.
- **Record B**: a zip of code uploaded straight to Zenodo with a journal article; no code repository.
- **Record C**: scripts released on GitHub and archived on Zenodo with its GitHub link.

## Results

| Field | A | B | C |
|---|---|---|---|
| name | Can be sent | Can be sent | Can be sent |
| author | Can be sent | Can be sent | Can be sent |
| description | Can be sent | Can be sent | Can be sent |
| license | Can be sent | Not sure | Can be sent |
| version | Held, not sent | Not sure | Held, not sent |
| identifier | Can be sent | Can be sent | Can be sent |
| softwareRequirements | Held, not sent | Not sure | Held, not sent |
| operatingSystem | Held, not sent | Not sure | Not held |
| codeRepository | Held, not sent | Not held | Held, not sent |
| referencePublication | Held, not sent | Held, not sent | Held, not sent |
| **Can be sent / Held / Not held / Not sure** | **5 / 5 / 0 / 0** | **4 / 1 / 1 / 4** | **5 / 4 / 1 / 0** |
| **In the archive?** | Yes: origin archived, release tag present | No | Origin known, but its only visit found nothing |
| **Updating** | Metadata-only, on the release's SWHID | Not sure: needs a first deposit | Not sure: nothing archived to point to |

## What it shows

1. **The core five mostly get through; the links to the rest do not.** In all three, the version, the code repository and the article never reach the export, even when the record page holds them (in relation fields such as "is part of", "is referenced by", "requires"). One mapping change in the export would turn most "held, not sent" into "can be sent".
2. **Without `codeRepository`, there is no metadata-only deposit.** The deposit needs a reference to the code in the archive. Record A's repository URL is on Zenodo but was lost when the record was copied; the export cannot build that reference on its own.
3. **A Zenodo DOI does not mean the code is archived.** Only 1 of the 3 is ready for a metadata-only deposit. Record B was never archived; record C's code repository was registered but not captured. The "copy, so metadata only" rule needs a second check: is the code actually in Software Heritage?
4. **Licences need a look.** Two of the three use a content licence (CC BY 4.0) for code, and in one of them the code's own `LICENSE` says GPL: the record and the code disagree (RSMD 6.3).
5. **The page is not the export.** Author identifiers (ORCID) are exported as text next to the names rather than attached to the creators; version numbers survive only inside titles and citations.

## How it gets lost

The metadata passes through four steps before it reaches the archive:

```text
submission form → DSpace record (dc.* fields) → OAI-PMH DataCite crosswalk → export → deposit client → Atom XML + CodeMeta → Software Heritage
```

A field can drop out at three points. Comparing the export with DSpace's own full record (the `dim` format on the same OAI-PMH endpoint, `metadataPrefix=dim`) shows which point it was for each field:

| Loss point | What happens | Fields in this demo |
|---|---|---|
| **1. No field** | The value is never stored on its own; it only appears inside other text | `version`: only inside the title ("… (0.5.0)") and the citation string ("…; Version 1.1; …") |
| **2. A field, but as text** | The value is stored, but as a sentence with the identifier buried inside | `codeRepository` and `referencePublication`: in `dc.relation.ispartof` and `dc.relation.isreferencedby` as citation strings ("Author. Title [Software]. https://github.com/…"); ORCIDs in a separate field as "Name [0000-…]", not linked to the author |
| **3. A field, not mapped** | The DataCite crosswalk has no rule for it, so the export leaves it out | the same relation fields, and `dc.relation.requires` ("Matlab"): `relatedIdentifiers` in the export is empty |

Two other cases sit outside the repository:

- **Upstream only**: dependencies and operating systems in record A exist only in the code (`pyproject.toml`). The repository never copied them.
- **Lost in the copy**: record A's link to its GitHub release is on Zenodo but was not carried over when the record was copied into the repository.

## How to move "held, not sent" to "can be sent"

**Route 1, on the repository side (lasting).** Store each value in its own field, as an identifier, and map it in the DataCite crosswalk (in DSpace, an XSLT file in the OAI configuration, `config/crosswalks/oai/metadataFormats/`). The deposit client then maps each DataCite element to its CodeMeta term.

| Field | Store it as | DataCite element | CodeMeta term in the deposit |
|---|---|---|---|
| version | its own field, e.g. `dc.description.version` = `1.1` | `<version>1.1</version>` | `codemeta:version` |
| codeRepository | the URL alone, with the release tag: `https://github.com/<owner>/<repo>/tree/v1.1` | `<relatedIdentifier relatedIdentifierType="URL" relationType="IsSupplementTo">` (the relation Zenodo uses for GitHub releases) | `codemeta:codeRepository`, and the origin for `swh:reference` |
| referencePublication | the article's DOI alone, e.g. `10.1000/182` | `<relatedIdentifier relatedIdentifierType="DOI" relationType="IsDescribedBy">` | `codemeta:referencePublication` |
| author ORCID | the ORCID on the same author entry, not in a separate text field | `<creator><creatorName>…</creatorName><nameIdentifier nameIdentifierScheme="ORCID" schemeURI="https://orcid.org">0000-0002-1825-0097</nameIdentifier></creator>` | `codemeta:author` with its identifier |
| softwareRequirements | the requirement and its version (`MATLAB R2023b`, `Python >=3.10`) | DataCite has no free-text field for it; a `Requires` relation needs an identifier, so map this field straight to CodeMeta in the deposit client | `codemeta:softwareRequirements` |

Existing records need a one-off clean-up: pull the URL or DOI out of each citation string, and the version number out of titles.

**Route 2, on the deposit-client side (quick).** Build the deposit from `dim`, which already carries every field, instead of from DataCite. Nothing changes in the repository, but the client has to parse the citation strings, and it ties the client to DSpace's internal field names. Good for a first test; route 1 is what makes the metadata reusable by everyone else who harvests the repository.

**"Not held" and "upstream only"** need a decision, not a mapping: add the field to the submission form (operating system, dependencies), copy it from the code's own metadata at submission, or leave it to the code. Once the code is archived, its own `codemeta.json`, `CITATION.cff` or `pyproject.toml` is in the archive with it.

## Next steps for a repository in this position

1. Add the version, the code repository URL (with the release tag) and the related article to the machine export.
2. Before choosing a metadata-only deposit, check that the code is in Software Heritage; if not, plan a first deposit.
3. Check that the licence on the record matches the code's, and prefer a software licence for code.
4. Attach ORCIDs to the creators in the export.

## Method note

The checks were done on 24 November 2026 with the [survey](https://lindangulopez.github.io/add-then-update/survey.html), reading the public record pages, exports, Zenodo records, code repositories and the Software Heritage API. The values were drafted with the help of Claude (Anthropic) and checked by the author, who made the final calls (the "Not sure" answers for record B are hers). Individual results are kept out of this repository.
