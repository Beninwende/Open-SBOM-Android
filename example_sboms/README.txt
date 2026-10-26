Open SBOMs for Android Apps - example SBOMs (pilot, October 2026)
=================================================================

Five example SBOMs in CycloneDX 1.6 JSON, built from the signed F-Droid APK
by static analysis only (nothing executed), with their validation output.
See pilot_report.pdf, section 6 (E3), for how they were built.

Files
-----
org.kde.kdeconnect_tp.cdx.json              KDE Connect 1.36.0: accessibility read + inject + INTERNET + overlay; not minified
com.typeassist.app.cdx.json                 TypeAssist 4.2.0: accessibility read, AI endpoint strings, llama.cpp/ggml native runtime; 84 % obfuscated
io.github.patricksmill.quicknotes.cdx.json  QuickNotes 1.0.7: openai-java SDK
dev.chungjungsoo.gptmobile.cdx.json         GPTMobile 0.9.1: LiteRT-LM SDK (com.google.ai.edge); 98 % obfuscated
com.android.talkback.cdx.json               TalkBack (build TfPu_release_15_1-2024_10_17): accessibility read + inject, AI endpoint string, no INTERNET

<name>.cdx.json.validation.txt              cyclonedx-cli output for each SBOM ("BOM validated successfully.")
_validation.tsv                             one line per SBOM: file name, exit code (0 = valid), time in ms

e3_negative_control.cdx.json                negative control: the TypeAssist SBOM with one unknown field
                                            added (metadata.component.bogusField)
e3_negative_control.txt                     cyclonedx-cli output for it: "BOM is not valid."

The apps are listed for the capability combinations they declare. These are
documented features of legitimate apps, not findings against them.

Validator
---------
cyclonedx-cli 0.33.1 (build b3cfa4b0edc356dad07e0b6e7ab6da0a94af0246)

Command used for each file:
  cyclonedx validate --input-file <file>.cdx.json --input-format json \
      --input-version v1_6 --fail-on-errors

Result: 5/5 valid; the negative control fails as expected.

Identifier and property choices
-------------------------------
- App identity (metadata.component): package URL type "generic",
    pkg:generic/<package>@<versionName>?download_url=<F-Droid APK URL>&checksum=sha256:<hash>
  plus a SHA-256 hash entry. "pkg:apk" is not used.
- Libraries found through Maven version files in the APK use pkg:maven/<group>/<artifact>@<version>
  (evidence technique: manifest-analysis). Libraries seen only by code namespace have no version
  (evidence technique: binary-analysis). Native .so files are listed as components.
- AI endpoints are listed under "services". They are strings found in the code, not observed
  at runtime.
- Capabilities (accessibility read/inject flags, overlay, INTERNET, AI signals, combinations,
  obfuscation share) are CycloneDX properties under the proposed namespace "apkcap:",
  e.g. apkcap:a11y:canRetrieveWindowContent, apkcap:combo:readInjectInternet.
  "apkcap:" is a proposal. It is NOT registered in the CycloneDX property taxonomy, and it
  does not use the reserved "cdx:" prefix. Each SBOM says so in the property
  apkcap:taxonomy:status.
- compositions.aggregate = "incomplete". The dependency graph is flat because the real
  graph is not known from the APK.
