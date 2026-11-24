# FAQ

Questions that may come up at the table. The answers use the same four sources as [WHY-THESE-FIELDS.md](WHY-THESE-FIELDS.md): the [RSMD guidelines](https://fair-impact.github.io/RSMD-guidelines/), [CodeMeta's `codemeta.json`](https://github.com/codemeta/codemeta/blob/master/codemeta.json), and the Software Heritage pages [Prepare your metadata](https://docs.softwareheritage.org/user/deposit/howto/prepare.html) and [The metadata-only deposit](https://docs.softwareheritage.org/devel/swh-deposit/specs/spec-meta-deposit.html).

## What to send

### Why is programming language key metadata, when it can be computed from the code?

It often can be: tools guess the language from file extensions and content. But RSMD still lists it with the descriptive metadata (RSMD 4.2), for three reasons:

- **A guess is not a statement.** A repository full of notebooks, generated files, vendored libraries or build scripts can be "mostly JavaScript" to a counting tool and "an R package" to its authors. Only the people who wrote it can say which language matters.
- **A metadata-only deposit has no code to compute from.** It is one XML file about something already in the archive, so whatever you want known has to be written in it.
- **Search happens before download.** Catalogues, registries and aggregators filter on the record, not on the files.

Rule of thumb: if a value can be computed reliably, sending it costs little and checks the computation. If it cannot, only you can provide it.

### Can't the dependencies (`softwareRequirements`) be computed too?

Partly. A `requirements.txt`, `pyproject.toml`, `DESCRIPTION` or `package.json` lists the libraries, and tools can read those. They do not say which language version, system libraries, hardware or data the software needs to run. RSMD asks for dependencies "in a machine-actionable format" (RSMD 7.1) and for the operating system and environment separately (RSMD 7.2), so the computed part is a start, not the whole answer.

### The code already has a `codemeta.json`. Isn't that enough?

RSMD asks for both: metadata in the code (intrinsic, RSMD 1.1) and a public metadata record (extrinsic, RSMD 1.2). With a deposit, the record is the Atom XML file sent with the code. The `codemeta.json` travels with the code into the archive; the XML is what describes this deposit. If they disagree, for example on the licence, RSMD asks that the code and the record match (RSMD 6.3).

### Why are only `name` and `author` required?

That is the deposit's minimum: the "prepare" page lists them as required and `version`, `description` and `license` as recommended. A deposit with only the minimum loads, but it is hard to find, cite or reuse. That is the point of "loaded is not checked".

### Why write the licence as an SPDX URL and not just "MIT"?

The URL (`https://spdx.org/licenses/MIT`) names one licence text without ambiguity, and CodeMeta's own `codemeta.json` writes its licence that way. RSMD also points to SPDX identifiers (RSMD 6.6). The Software Heritage deposit examples write the licence by name, inside `<codemeta:license><codemeta:name>`. Either way, use one exact licence, and the same one in the code and in the record.

### Isn't the version already in the Git tags?

It may be, but the deposit does not read your tags. `codemeta:version` is how Software Heritage tells apart several deposits of the same origin. If it is missing, the deposit uses a number counting the deposits made for that origin, which says nothing to a reader.

### Which identifier goes in `identifier`?

Your own persistent identifier for the software, such as a DOI, an ARK or a HAL identifier; the deposit spec's example says "other identifier, DOI, ARK". The SWHID is what the archive gives back to you after the deposit.

### Who goes in `author`?

The people (or the collective) who wrote the software, with an ORCID or another person identifier where there is one (RSMD 5.3). Note the two "authors" in a deposit file: in the spec's example, the Atom `<author>` is the depositing platform (HAL), while `<codemeta:author>` holds the software's authors.

### Why are keywords, domain and dates not on the sheet?

Only to fit the exercise into twenty minutes. RSMD 4.2 lists them, CodeMeta has terms for them, and the Software Heritage examples use several. They are worth sending.

## Updating

### I made a mistake in a deposit. How do I fix it?

Make a new deposit with the same `codemeta:version`. The next snapshot shows only the latest deposit with that version number; the earlier one stays in the archive under its SWHID.

### Do I have to send the code again to fix or add metadata?

No. A metadata-only deposit sends one Atom XML file about an origin or a SWHID that is already in the archive. It can point to an origin, snapshot, release, revision, directory or content, but not to a range of lines.

### When is it a new version and when a metadata-only update?

New code means a new version (`swh:add_to_origin`, with a new `codemeta:version`). Same code, better description means a metadata-only deposit (`swh:reference`).

## Formats

### Which CodeMeta version should I use?

The deposit accepts CodeMeta v2 for now; the "prepare" page says v3 will follow. CodeMeta's own file already uses 4.0. All ten fields on the sheet exist in v2.

### Is this list a standard?

No. The ten fields are a choice for this session, built from the deposit's required and recommended fields and five RSMD questions. See [WHY-THESE-FIELDS.md](WHY-THESE-FIELDS.md).
