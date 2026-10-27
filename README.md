# Chromium ARM32 build for Android 8.1 (MT6739 / armeabi-v7a)

Builds a release Chromium APK (`chrome_public_apk`) for 32-bit ARM (ARMv7, softfp)
targeting Android 8.1 (API 27) via GitHub Actions.

## Why GitHub-hosted runners can't finish this

- `ubuntu-22.04` runner: 4 vCPU, 16 GB RAM, ~14 GB usable SSD (not 200+ GB).
- Chromium `src` + build output needs ~100-150 GB. `gclient sync` will fail with ENOSPC.
- ThinLTO link needs 16-32 GB RAM; runner will OOM/thrash even with swap.
- `is_official_build=true` requires full checkout + official toolchain.

## What you actually need

A **self-hosted runner** with:

| Resource | Minimum |
|---|---|
| CPU | 8 cores |
| RAM | 32 GB |
| Disk | 200 GB SSD |
| OS | Ubuntu 22.04 x86_64 |

## Usage

1. Register a self-hosted runner on a machine meeting the specs above.
2. Edit `.github/workflows/build-chromium.yml`: change `runs-on` to `[self-hosted, linux, x64]`.
3. Run the workflow manually (Actions → Build Chromium ARM32 → Run workflow).
4. Download `chromium-arm32-apk` artifact.

## Notes on args.gn

Some flags from the original request are invalid or removed in current Chromium and
are stripped by the workflow before `gn gen`:

- `enable_remoting`, `enable_cardboard` — removed.
- `enable_arcore`, `enable_gvr_services` — never valid GN args.
- `enable_webrtc = false` — removed; WebRTC cannot be disabled cleanly.
- `safe_browsing_mode = 0` — deprecated; use `safe_browsing_mode = 0` only on old branches.

`chrome_public_manifest_package` is valid on upstream Chromium.