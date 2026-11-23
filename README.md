<div align="center">

# Discipulus Nightly

**Unofficial automated builds of the latest [Discipulus](https://github.com/DiscipulusApp/Discipulus) source.**

[![Build status](https://github.com/blacklizardcode/discipulus-nightly/actions/workflows/nightly.yml/badge.svg)](https://github.com/blacklizardcode/discipulus-nightly/actions/workflows/nightly.yml)
[![Latest release](https://img.shields.io/github/v/release/blacklizardcode/discipulus-nightly?label=latest%20build)](https://github.com/blacklizardcode/discipulus-nightly/releases/latest)

[![Add to Obtainium](https://img.shields.io/badge/Obtainium-Add%20nightly-3DDC84?style=for-the-badge&logo=android&logoColor=white)](obtainium://add?url=https%3A%2F%2Fgithub.com%2Fblacklizardcode%2Fdiscipulus-nightly)

</div>

---

## What is this?

This repository automatically builds the newest commit on `DiscipulusApp/Discipulus` and publishes the resulting platform packages as a GitHub Release. It does not modify the upstream application source.

The workflow checks upstream **once per hour**. A new release is created only when the upstream `main` branch has a commit that has not already been built. You can also start a check manually from the **Actions** tab.

> This is an unofficial build service. It is not operated by, endorsed by, or affiliated with the Discipulus developers or Iddink Group. For the original project, issues and official releases, visit [DiscipulusApp/Discipulus](https://github.com/DiscipulusApp/Discipulus).

## Create the GitHub repository

Create a **public, empty** repository named `discipulus-nightly` under the `blacklizardcode` account. Use the description below, then upload the contents of this starter kit to the repository root (including the hidden `.github` directory). Do not initialize the new repository with another README or license, because this kit already includes its README.

> Unofficial hourly CI builds of the latest Discipulus commit for Android, Wear OS, Windows, Linux and Apple platforms, published as GitHub Releases.

The workflow needs GitHub Actions enabled and requests `contents: write` only in the release-publishing job so it can create GitHub Releases.

## Downloads

Open **[Releases](https://github.com/blacklizardcode/discipulus-nightly/releases/latest)** and choose the asset for your device:

| Platform | Release asset | Notes |
|---|---|---|
| Android phone / tablet | `Discipulus-android-phone-…apk` | APK for direct installation or Obtainium |
| Android phone / tablet | `Discipulus-android-phone-…aab` | Android App Bundle; mainly for Play distribution workflows |
| Wear OS | `Discipulus-wearos-…apk` | Separate Wear OS companion package |
| Windows | `Discipulus-windows-…zip` | Extract the complete folder and run `Discipulus.exe` |
| Linux | `Discipulus-linux-…tar.gz` | Extract the archive; the executable and runtime files are in `bundle/` |
| macOS | `Discipulus-macos-…zip` | Extract `Discipulus.app`; unsigned and not notarized |
| iOS + watchOS | `Discipulus-ios-watchos-unsigned-…xcarchive.zip` | Unsigned Xcode archive; the Apple Watch app is embedded in this archive |

Each release is built from one pinned upstream commit. The release notes link to that exact source revision.

## Obtainium (Android)

Install [Obtainium](https://github.com/ImranR98/Obtainium) on your Android device, then use the **Add to Obtainium** link above. It opens Obtainium's Add App screen with this repository pre-filled. Choose the `Discipulus-android-phone-…apk` asset as the update file; do not select the Wear OS APK for a phone installation.

**Android signing matters:** the upstream Android package uses the application ID `dev.harrydekat.discipulus`. A nightly build signed with a different key cannot update an installation signed by the official Play Store key, and Android will not install it over that app. Uninstall the official app first if you are switching to this unofficial build. To receive updates from one nightly build to the next, configure the stable signing key below before relying on Obtainium.

### Configure a stable Android signing key

Without signing secrets, the workflow falls back to the GitHub runner's debug signing configuration. Hosted runners are temporary, so that fallback key is not stable between runs. For reliable Obtainium updates, create your own keystore and save it as repository Actions secrets:

| Secret | Value |
|---|---|
| `ANDROID_KEYSTORE_BASE64` | Base64-encoded contents of your `.jks` keystore |
| `CM_KEYSTORE_PASSWORD` | Keystore password |
| `CM_KEY_ALIAS` | Key alias inside the keystore |
| `CM_KEY_PASSWORD` | Key password |

Example commands to create a key and encode it on Linux:

```bash
keytool -genkeypair -v \
  -keystore discipulus-nightly.jks \
  -alias discipulus-nightly \
  -keyalg RSA -keysize 2048 -validity 10000

base64 -w 0 discipulus-nightly.jks
```

Copy the base64 output into `ANDROID_KEYSTORE_BASE64`. Add the other three values using **Settings → Secrets and variables → Actions → New repository secret**. Keep a secure backup of the keystore: losing it means you will not be able to sign updates with the same key. Never commit the keystore or its passwords to the repository.

## How the workflow works

1. Reads the latest commit SHA from upstream `main`.
2. Checks whether a release for that exact SHA already exists here.
3. Checks out the upstream source at that immutable SHA, including its submodules.
4. Builds the supported platforms on the appropriate GitHub-hosted runners.
5. Publishes all successful build artifacts together in one GitHub Release.

### Apple signing limitations

The iOS/watchOS artifact is an **unsigned `.xcarchive`**, not a ready-to-install IPA. The workflow can compile the iOS app and its embedded watchOS app without Apple signing credentials, but installing on devices or distributing through TestFlight requires appropriate Apple certificates and provisioning profiles. The macOS app is also unsigned and not notarized, so macOS may show a Gatekeeper warning.

## Source and license

Discipulus is maintained at [DiscipulusApp/Discipulus](https://github.com/DiscipulusApp/Discipulus) and is licensed under **GNU GPL-3.0**. The nightly releases are built from upstream source without application-code modifications. Each release identifies the exact upstream commit so that the corresponding source can be inspected or obtained from that repository. See the upstream [LICENSE](https://github.com/DiscipulusApp/Discipulus/blob/main/LICENSE).

The workflow and README in this repository are provided for the purpose of automating those builds. Discipulus names and branding belong to their respective owners.

---

<sub>Built automatically from upstream. Nightly builds may contain bugs or unfinished changes; use them at your own risk.</sub>
