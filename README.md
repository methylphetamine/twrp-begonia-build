# TWRP build for Redmi Note 8 Pro (`begonia`)

GitHub Actions recipe that compiles **TWRP** `recovery.img` for the Xiaomi
Redmi Note 8 Pro, codename `begonia`.

The device tree is the PBRP/SHRP begonia tree with a TWRP product
(`twrp_begonia`) that inherits `vendor/twrp/config/common.mk`. It keeps the
working prebuilt kernel, DTB, DTBO, fstab and decryption blobs.

## What this builds

| | |
|---|---|
| TWRP manifest | `minimal-manifest-twrp/platform_manifest_twrp_aosp` @ `twrp-12.1` |
| TWRP recovery source | `TeamWin/android_bootable_recovery` @ `android-12.1` |
| TWRP vendor config | `TeamWin/android_vendor_twrp` @ `android-12.1` |
| Device tree | `methylphetamine/device_xiaomi_begonia-twrp` @ `twrp-12.1` or `unified` |
| Product / lunch | `twrp_begonia` / `twrp_begonia-eng` |
| Make target | `recoveryimage` |
| Output | `out/target/product/begonia/recovery.img` |

`twrp-14.1` is a newer upstream branch, but this tree is Android 12.1 and uses
the 12.1 recovery source. The 14.x branch would need a device-tree port.

## Usage

1. **Actions → TWRP build (begonia) → Run workflow**
2. Options:
   - `makefile` — default `twrp_begonia-eng`
   - `build_target` — default `recoveryimage`
   - `manifest_branch` — default `twrp-12.1`
   - `device_branch` — device tree branch, default `twrp-12.1`; set it to
     `unified` to build the dual-layout tree (dynamic+erofs **and**
     non-dynamic+ext4/f2fs)
   - `jobs` — default `4` (the runner has 4 vCPUs)
   - `ccache` — default `true`
   - `publish_release` — attach the image to a GitHub release
3. When the job goes green, download `twrp-begonia-recovery` from the run's
   **Artifacts** section.

Every run does a fresh `repo init` / `repo sync`, and the **Record TWRP source
revisions** step prints the exact commits used.

## Publishing

After a successful compile the workflow:

1. uploads `recovery.img` as the `twrp-begonia-recovery` Actions artifact;
2. uploads it anonymously to Gofile and prints the download page;
3. sends it to Telegram as a document when it is under Telegram's 50 MB bot
   limit, otherwise sends the Gofile link and SHA-256.

Telegram publishing uses the repository secrets `TELEGRAM_BOT_TOKEN` and
`TELEGRAM_CHAT_ID`. Gofile needs no credentials. Both uploads are best-effort
and never fail the build.

## Device tree notes

The tree is self-contained: kernel, DTB, DTBO, keymaster/gatekeeper blobs and
recovery fstab are under `prebuilt/` and `recovery/root/`, so no kernel build
or proprietary vendor tree is needed. `ALLOW_MISSING_DEPENDENCIES` is set by
the workflow for the minimal TWRP manifest.

## Repository layout

```
.github/workflows/build.yml   the CI recipe
local_manifests/begonia.xml   device tree registration
```
