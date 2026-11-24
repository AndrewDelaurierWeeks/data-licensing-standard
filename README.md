# The Data Licensing Standard for AI

Requirements, evidence and scoring for AI training data vendors. Version 0.1 (v0.1), public draft for adoption, 2026-11-24. Andrew Delaurier Weeks.

Licensed datasets, human data, synthetic data, sensor records, evaluation data and RL environments, and the brokers, marketplaces and vendors that supply them.

The edition is dated 2026-11-24, the UTC date of its seal. It supersedes the edition sealed on 2026-11-16, published under an earlier name and earlier file names, whose source has the SHA-256 `626b49613704368ae40b17d5cbbe20dfd3559961883d25cb8a1e9366dd21bf86`; that edition is archived at https://github.com/AndrewDelaurierWeeks/data-licensing-standard/commit/9ef537703d4ef6d2fe7114da85f2dd584f31ba78.

Written and published by the author in a personal capacity. No employer or client of the author took part in it, reviewed it, or is bound by it, and no employer of the author has any role in the practice described in Part 6. Reading it forms no engagement and no professional relationship. It is a procurement standard; it contains no legal advice and is no audit, certification or assurance of any result.

**Conflict disclosure.** The author expects to perform attestation work of the kind Requirement 4 describes through a practice of his own, described in Part 6, and states its rules there; he is also building other businesses in this market, whose work Part 6, rule 3 bars the practice from attesting. Requirement 4 is met by any independent party, including existing audit and assurance firms; no attester receives preference under this document, and the author's own practice is subject to Part 5 like any other. Those other businesses may supply evidence of kinds this document names, among them logs and receipts of the form R9's M line names. Evidence is scored on the tier line it meets, whoever supplied it, and no source of evidence receives preference under this document.

## The files

| File | What it is |
|---|---|
| `TDLS-AI-v0.1.pdf` | The document, 83 pages |
| `TDLS-AI-v0.1.md` | The Markdown source of the document's text |
| `TDLS-AI-v0.1.sha256` | The SHA-256 of the PDF and of the source text |
| `TDLS-AI-v0.1.tsr` | The RFC 3161 time-stamp token over the SHA-256 of the PDF |
| `TDLS-AI-v0.1.md.tsr` | The RFC 3161 time-stamp token over the SHA-256 of the source text |
| `schema/tdls-ai-vendor-declaration.schema.json` | The vendor declaration schema (JSON Schema 2020-12), version 0.3.0, `https://datalicensingstandard.org/schema/0.3.0/tdls-ai-vendor-declaration.schema.json` |
| `schema/example-declaration.json` | An example declaration |
| `schema/validate.py` | The validator, with the converter for a record in the form of OASIS Data Provenance Metadata Version 1.0, Committee Specification Draft 01 |
| `schema/crosswalk-0.3.0.json` | The crosswalk of Annex A, machine-readable |
| `schema/LICENSE` | The MIT License of the schema, the example, the validator and the crosswalk file |
| `.gitattributes` | Tells Git to keep line ends as stored, so a checkout holds the hashed bytes of the source text |

## Verify the seal

The two tokens were issued by DigiCert's time-stamp authority, signing certificate DigiCert SHA256 RSA4096 Timestamp Responder 2026 1, policy 2.16.840.1.114412.7.1, on 2026-11-24, at 19:19:59 UTC for the PDF and at 19:21:04 UTC for the source text.

Check both files against `TDLS-AI-v0.1.sha256`:

```
sha256sum -c TDLS-AI-v0.1.sha256
```

It prints `TDLS-AI-v0.1.pdf: OK` and `TDLS-AI-v0.1.md: OK`.

Verify each token against its file. The root certificate is the self-signed DigiCert Trusted Root G4, SHA-256 fingerprint `55:2F:7B:DC:F1:A7:AF:9E:6C:E6:72:01:7F:4F:12:AB:F7:72:40:C7:8E:76:1A:C2:03:D1:D9:D2:0A:C8:99:88`. DigiCert lists this fingerprint and offers a PEM file of the root on its page DigiCert Trusted Root Authority Certificates, https://knowledge.digicert.com/general-information/digicert-trusted-root-authority-certificates (read 2026-11-17). On Debian and Ubuntu the system store holds it at the path shown:

```
openssl ts -verify -data TDLS-AI-v0.1.pdf -in TDLS-AI-v0.1.tsr -CAfile /etc/ssl/certs/DigiCert_Trusted_Root_G4.pem
openssl ts -verify -data TDLS-AI-v0.1.md -in TDLS-AI-v0.1.md.tsr -CAfile /etc/ssl/certs/DigiCert_Trusted_Root_G4.pem
```

Each command prints `Verification: OK`, after a line on standard error that names OpenSSL's configuration file.

On another system, use a PEM file of this root certificate in place of that path. This command, with the path of that file in place of FILE, prints the certificate's fingerprint:

```
openssl x509 -in FILE -noout -fingerprint -sha256
```

To read each token's time (the line `Time stamp:`):

```
openssl ts -reply -in TDLS-AI-v0.1.tsr -text
openssl ts -reply -in TDLS-AI-v0.1.md.tsr -text
```

That printout's `TSA` field reads `unspecified`; the authority is named in the signing certificate inside the token, and this command prints the token's certificates:

```
openssl ts -reply -in TDLS-AI-v0.1.tsr -token_out | openssl pkcs7 -inform DER -print_certs -noout
```

Among the certificates printed is one named DigiCert Trusted Root G4 and issued by DigiCert Assured ID Root CA. It is a cross-signed certificate for the same key, carried in the token; the verify commands trust the self-signed root with the fingerprint given above.

## Validate a declaration

```
cd schema
python3 -m venv .venv
.venv/bin/pip install jsonschema rfc3339-validator
.venv/bin/python validate.py tdls-ai-vendor-declaration.schema.json example-declaration.json
```

Debian 12 and Ubuntu 24.04 refuse `pip install` into the system Python (PEP 668), so these commands install into a virtual environment; on Debian and Ubuntu, `python3 -m venv` needs the python3-venv package. The commands were run with jsonschema 4.26.0 and rfc3339-validator 0.1.4 on Python 3.12 and 3.13. The last command prints `valid: example-declaration.json conforms to TDLS-AI Vendor Declaration (The Data Licensing Standard for AI, v0.1) (https://datalicensingstandard.org/schema/0.3.0/tdls-ai-vendor-declaration.schema.json)`. The validator exits 0 when the declaration conforms to the schema, 1 with every error listed when it does not, and 2 when it cannot run.

To turn a record in the form of OASIS Data Provenance Metadata Version 1.0, Committee Specification Draft 01 (07 May 2026), into a partial declaration, put the path of the record in place of RECORD.json; the release ships no sample record:

```
.venv/bin/python validate.py convert RECORD.json
```

It prints the partial declaration on standard output and, on standard error, one line for each field it converted, with that field's class in the crosswalk, carried or stricter, and a last line saying the result is partial and unsigned. The result is incomplete and unsigned, and it meets no tier of the standard until a named person at the vendor completes and signs it.

## License

Text under Creative Commons Attribution 4.0 International, excluding material quoted from the sources named in Annex B, wherever it appears, and the field names and section headings of the outside specifications in Annex A, in which this license grants nothing and any rights stay with their holders; the schema, example, validator and crosswalk file under the MIT License, which covers the author's own work in those files and no name, heading or text of an outside specification; the cover engraving, created by the author, of a carved neoclassical anthropomorphic mascaron sculpted in high relief, under the same CC BY 4.0 license as the text. A buyer may reproduce Parts 4 and 5 in its own procurement documents with attribution.

## Cite as

Weeks, A. D. (2026). The Data Licensing Standard for AI (TDLS-AI) v0.1. Sealed on the date in the published time-stamp token. A requirement is cited by short name, version and number, as in TDLS-AI v0.1 R8; a question, as in TDLS-AI v0.1 R8, question 10. The schema (Annex A) is cited by its `$id` and version.
