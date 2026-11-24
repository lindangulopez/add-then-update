# Add, then update

Research software metadata in deposits: a small kit for one unconference session at the Community Workshop, 25 November 2026.

**Site**: https://lindangulopez.github.io/add-then-update/

- [`survey.html`](survey.html): map one software record against ten CodeMeta fields (sent to deposit, can be sent, held but not sent, not held). It runs in the browser only: nothing is sent or stored on a server; answers can be copied or saved as Markdown.
- [`slides.html`](slides.html): ten slides: the session, the logic (four statuses, where it gets lost, how to fix it) and the demo (arrow keys, `f` for full screen). A QR code links to the survey.
- [`demo/`](demo/README.md): a worked example on three anonymised records, with the logic and the results.
- [`print/sheets.pdf`](print/sheets.pdf): the mapping sheet and the ten-field card, A4. Built from `print/sheets.html`.

## The ten fields

Five core: `name`, `author`, `description`, `license`, `version`. Five more: `identifier`, `softwareRequirements`, `operatingSystem`, `codeRepository`, `referencePublication`. All are [CodeMeta](https://codemeta.github.io/) terms. Why these ten, against the RSMD guidelines, CodeMeta and the Software Heritage deposit docs: [WHY-THESE-FIELDS.md](WHY-THESE-FIELDS.md). Questions from the table: [FAQ.md](FAQ.md).

## Rebuild the PDF

```bash
google-chrome --headless --no-pdf-header-footer --print-to-pdf=print/sheets.pdf print/sheets.html
```

## Licence

Text and printouts: CC BY 4.0. Code (HTML, CSS, JavaScript): MIT, see [LICENSE](LICENSE).

Written with the help of Claude (Anthropic).
