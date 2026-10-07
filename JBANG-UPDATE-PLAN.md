# JBang Update Plan

## Version check and execution status

Checked and updated on 2026-10-07:

- Bundled JBang before update in `.jbang/jbang.jar`: **0.138.0**
- Latest GitHub release: **v0.142.0**, published 2026-09-22 ([release](https://github.com/jbangdev/jbang/releases/tag/v0.142.0))
- Update target: **v0.142.0**
- Installed JBang after update: **0.142.0**

The v0.142.0 fat JAR replaced `.jbang/jbang.jar`. The `jbang`, `jbang.cmd`, and `jbang.ps1` launchers were fetched from the matching upstream release and replaced in the repository root. The Windows x64 release archive passed its published SHA-256 check before extraction.

Version checks succeeded for the JAR and both Windows launchers (`jbang.ps1 version` and `jbang.cmd version`).

## Update scope

Fetch the launcher scripts from the JBang repository at the selected release tag, and update the bundled runtime from that same release:

- `jbang` — `src/main/scripts/jbang`
- `jbang.cmd` — `src/main/scripts/jbang.cmd`
- `jbang.ps1` — `src/main/scripts/jbang.ps1`
- `.jbang/jbang.jar` — from the v0.142.0 Windows release archive, if the archive layout includes the bundled JAR

Raw script URLs:

- `https://raw.githubusercontent.com/jbangdev/jbang/v0.142.0/src/main/scripts/jbang`
- `https://raw.githubusercontent.com/jbangdev/jbang/v0.142.0/src/main/scripts/jbang.cmd`
- `https://raw.githubusercontent.com/jbangdev/jbang/v0.142.0/src/main/scripts/jbang.ps1`

Windows runtime archive:

- `https://github.com/jbangdev/jbang/releases/download/v0.142.0/jbang-0.142.0-windows-x64.zip`
- Verify the downloaded archive against its matching `.sha256` file (or the release `checksums_sha256.txt`) before extracting it.

## Update procedure

1. Review the existing launcher scripts and identify repository-specific changes before replacing them with upstream versions.
2. Download all three scripts with `curl.exe -fL` into temporary files, using the **same pinned release tag** in every URL. Fail the update on an HTTP or download error.
3. Download the v0.142.0 Windows x64 release archive and its published checksum; verify the checksum before extraction.
4. Inspect the archive layout, then update `.jbang/jbang.jar` from the matching release if appropriate. Keep the launcher scripts and runtime on the same version.
5. Replace the checked-in files only after downloads and verification succeed. Review `git diff` to confirm the intended upstream changes and retain any project-specific behavior that must not be lost.
6. Validate on Windows:
   - Run `jbang.cmd version` and confirm it reports `0.142.0`.
   - Run `jbang.ps1 version` and confirm it reports `0.142.0`.
   - Smoke-test a simple Java source through the launcher and confirm the process exits successfully.
   - Confirm no launcher accidentally downloads the unpinned `latest` release when the bundled runtime is expected to be used.
7. Review the final diff and update this document’s target version and date when applying a future JBang update.

## Update command pattern

For each script, use a temporary destination and only move it into place after `curl.exe` succeeds. Example for the Windows launcher:

```powershell
curl.exe -fL --retry 3 `
  -o .\jbang.cmd.new `
  https://raw.githubusercontent.com/jbangdev/jbang/v0.142.0/src/main/scripts/jbang.cmd
```

Use the corresponding pinned raw URL for the other scripts. Do not use a floating `main` URL for a reproducible update.
