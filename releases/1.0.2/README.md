# Release 1.0.2: extensionless artifact test

## Current test

- The selected artifact is `ota`, with no filename extension.
- Its contents are an exact copy of the original valid local `D:/AI/speakerOta/ota.rbl`. It is not the corrupted file from the preceding hash-rejection test.
- Size: 3,538,944 bytes.
- SHA-256: `f2bbeda5cd658dbf9e4b4c7901b425738826c9e4c9f3b74dcee546c5942469a5`.
- `release.json` declares filename `ota`; `ota.sha256` contains the matching hash and filename.
- The production manifest selects version 1.0.2 and must use a commit-pinned URL ending in `/releases/1.0.2/ota`.

This isolates handling of a missing `.rbl` extension: size, hash, and original binary contents are valid. Observe whether the device rejects the URL/filename or accepts the content. Removing the filename extension does not itself corrupt the RBL format. A device that does not enforce the suffix may perform a real upgrade to 1.0.2; rejection is not established by file renaming alone.

Use a speaker currently running 1.0.1. A speaker already running 1.0.2 may skip downloading because the versions match. Device layout/bootloader compatibility with this artifact still needs bench verification.

## Previous test and recovery

The separate `ota.rbl` file currently retained in this directory is the previous intentionally corrupted fixture (one bit flipped at offset `0x100`). Its sidecar `ota.rbl.sha256` intentionally contains the expected original hash. The current manifest does not select that corrupted file.

The original good `ota.rbl` remains available at artifact commit `2733f0b2f38e9606a64b78a78b009e3445fc7629`, path `releases/1.0.2/ota.rbl`. Restore the manifest URL and release filename to that original file to return to normal OTA distribution.

## Build provenance and layout

`source_commit` and `source_dirty` remain null because original build provenance was not supplied. Do not infer them from commits in this artifact repository.

The 1.0.2 and 1.0.0 original containers both have length 3,538,944 bytes and an RBL header at `0x35F000`; their hashes differ. The older 0.1.x containers use length 3,342,336 bytes and header offset `0x32F000`. Equal padded container lengths do not imply equal firmware contents.
