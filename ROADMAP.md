# Roadmap

## Post-1.0

- **Publish the Java packages to Maven Central (in addition to, or instead of, GitHub Packages).**
  GitHub Packages' Maven registry doesn't render a description or README on the package page —
  confirmed empirically: `pagmonet4j`, `pagmonet4j-kotlin`, and `pagmonet4j-ipopt` all show "No
  description available yet" despite each POM already setting `description` (see the three
  `build.gradle.kts` files under `PagmoNet4j/`). It also requires a `read:packages` token even for
  public packages, which the READMEs already call out as friction. Maven Central renders the POM
  description properly and needs no auth for public reads, so it would fix both — but it needs a
  Sonatype Central Publisher account and GPG-signed artifacts, so it's a real setup lift, not a
  quick fix. Not worth doing in the final push to 1.0.

- **Wire the native wrapper's version to the release tag.** `native/CMakeLists.txt:2` —
  `project(PagmoWrapper VERSION 1.0.0)` — is hardcoded, a third version source independent of
  `Directory.Build.props`' `<Version>` and `gradle.properties`' `version=`, both of which CI already
  overrides per-release via `-p:Version=` / `-Pversion=`. Consequences: the Linux/macOS SONAME
  (`libPagmoWrapper.so.1.0.0`) reflects whatever's hardcoded here, not the actual release; Windows
  has no `.rc` / `VERSIONINFO` resource at all, so `PagmoWrapper.dll` shows no version in its
  Details tab on any release. Fix: pass the resolved version into `build-native.ps1` /
  `build-native.sh` as a `-D` (e.g. `-DPAGMONET_VERSION=`) sourced from the same tag CI already
  resolves, and add a minimal Windows `.rc` so the DLL gets a real `FILEVERSION`/`PRODUCTVERSION`.
  Correct by coincidence for 1.0.0; will silently go stale the first time the version bumps without
  someone remembering to hand-edit this file.
