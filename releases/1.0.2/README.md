# Release 1.0.2

## Artifact and provenance

- `ota.rbl` was copied byte-for-byte from the supplied local `D:\AI\speakerOta\ota.rbl`.
- Size: **3,538,944 bytes**.
- SHA-256: `f2bbeda5cd658dbf9e4b4c7901b425738826c9e4c9f3b74dcee546c5942469a5`.
- AP OTA strings and CP1/CP2 release marker strings in the binary contain `1.0.2`. These strings are supporting evidence, not a substitute for reading the running version after boot.
- The original source repository commit and dirty working-tree state were not supplied. `source_commit` and `source_dirty` are therefore explicitly `null` (unknown). They must not be copied from 1.0.0 or replaced with the artifact repository commit. Replace them only with the actual build provenance.

## Size and header observations

| Releases | Actual file size | Observed RBL header offset |
|---|---:|---:|
| 0.1.0 through 0.1.4 | 3,342,336 | `0x32F000` |
| 1.0.0 and 1.0.2 | 3,538,944 | `0x35F000` |

The 1.0.0 and 1.0.2 files have different SHA-256 digests despite identical lengths. Both contain an RBL payload length of 3,294,972 bytes, padding, and a header in the final 4 KiB region. A fixed-size padded container can retain the same length across firmware versions. Do not change `size` to a different value to distinguish versions: it must equal the bytes served.

For the supplied 1.0.2 file, RBL magic at `0x35F000`, CRC32 of the first 92 header bytes, and CRC32 of the payload length recorded in the header were checked and match the stored values. This is an artifact integrity check, not proof of the device bootloader's complete verification or physical Flash CRC behavior. The RBL header version field is `2` in both 1.0.0 and 1.0.2; application version is carried separately. The artifact bytes have not been patched.

## OTA publication and device requirements

The production manifest `manifests/lumi-assistant-speaker.json` selects application version `1.0.2`, uses the real file length and full-file SHA-256, and points to the immutable artifact commit `2733f0b2f38e9606a64b78a78b009e3445fc7629`. That commit already contains these exact artifact bytes. Later provenance/documentation commits do not require changing this URL.

For a speaker running 1.0.1 to update:

1. It must actually fetch this production manifest URL (not a different repository/branch or a cached test manifest).
2. Its runtime/bootloader and partition layout must support the **3,538,944-byte container** with header at **`0x35F000`**. The available `ota.md` describes the older 3,342,336-byte layout; it cannot establish 1.0.1 compatibility. No 1.0.1 image, source, or complete device boot log was supplied for independent confirmation.
3. Product, HTTPS/TLS, immutable URL, Content-Length, file size, SHA-256, health prechecks and inactive-slot write verification must pass. A numerically newer version alone is insufficient.
4. After candidate boot, verify actual running version 1.0.2, current/pending slot equality, health success, correct confirm command, and pending cleared to 255.
5. Reboot again and verify version remains 1.0.2 and the OTA decision skips downloading the equal version.

The release files and manifest have been checked; physical OTA success and compatibility of a specific 1.0.1 speaker still require the device checks above.
