# Open SBOMs for Android Apps

Open, versioned [CycloneDX](https://cyclonedx.org/) Software Bills of Materials (SBOMs) for Android apps, built by **static analysis of signed APKs** (nothing executed).

This repository publishes **pilot artifacts**: schema-valid SBOMs, example files, and experiment data. A packaged scanner and full F-Droid catalogue run are planned as next steps.

## What a SBOM includes

- Third-party libraries (Maven coordinates and versions when recoverable)
- Native libraries (`.so`)
- AI-related signals (SDK namespaces, endpoint strings, native runtimes)
- Declared capabilities (accessibility read/inject, overlay, `INTERNET`, combinations) as proposed CycloneDX properties under the `apkcap:` namespace

Apps are listed for the capability combinations they declare. These are documented features of legitimate apps, not findings against them.

## Repository layout

| Path | Contents |
|------|----------|
| [`pilot_report.pdf`](pilot_report.pdf) | Pilot setup, sample, six experiments (E1–E6), and limits |
| [`example_sboms/`](example_sboms/) | Five CycloneDX 1.6 JSON SBOMs + validation output |
| [`open-sboms-android/data/`](open-sboms-android/data/) | Pilot dataset: 306 SBOMs and experiment summaries |

## Example SBOMs

See [`example_sboms/README.txt`](example_sboms/README.txt). Highlights:

- **KDE Connect** — accessibility read + inject + Internet + overlay
- **TypeAssist** — accessibility read, AI endpoint strings, llama.cpp/ggml native runtime
- **QuickNotes** — openai-java SDK
- **GPTMobile** — LiteRT-LM SDK; heavily obfuscated
- **TalkBack** — accessibility read + inject; no Internet

All five validate with `cyclonedx-cli` against CycloneDX 1.6. A negative-control SBOM with an unknown field fails as expected.

```bash
cyclonedx validate --input-file example_sboms/org.kde.kdeconnect_tp.cdx.json \
  --input-format json --input-version v1_6 --fail-on-errors
```

## Pilot snapshot (October 2026)

- **306** F-Droid APKs analysed; SBOMs are schema-valid CycloneDX 1.6
- **84** APKs declare an accessibility service (F-Droid index records far fewer)
- In **471** updates of **121** apps, **28.5%** changed at least one versioned Maven library
- Against declared dependencies of 9 apps: ~52% exact name+version, ~76% at vendor level (obfuscation is the main gap)

Details and methodology: [`pilot_report.pdf`](pilot_report.pdf).

## Licence

- Code (when published): [Apache-2.0](LICENSE)
- Pilot data and SBOMs: intended as open data (CC0-1.0)

## Status

Pilot data and documentation are public. The command-line tool and catalogue pipeline are not in this repository yet.
