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

## Next steps for a repository in this position

1. Add the version, the code repository URL (with the release tag) and the related article to the machine export.
2. Before choosing a metadata-only deposit, check that the code is in Software Heritage; if not, plan a first deposit.
3. Check that the licence on the record matches the code's, and prefer a software licence for code.
4. Attach ORCIDs to the creators in the export.

## Method note

The checks were done on 24 November 2026 with the [survey](https://lindangulopez.github.io/add-then-update/survey.html), reading the public record pages, exports, Zenodo records, code repositories and the Software Heritage API. The values were drafted with the help of Claude (Anthropic) and checked by the author, who made the final calls (the "Not sure" answers for record B are hers). Individual results are kept out of this repository.
