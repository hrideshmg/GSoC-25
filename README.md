# Google Summer Of Code 2025 - Final Report
## CCExtractor v1.00 Release
Organization: [CCextractor](https://summerofcode.withgoogle.com/programs/2025/organizations/ccextractor-development)<br>
Project: [1.00 Release](https://summerofcode.withgoogle.com/programs/2025/projects/t4gOHYbv)<br>
Contributor: Hridesh MG

---

## Summary
CCExtractor’s last stable release (v0.94) came out in 2021, and since then, the codebase has undergone significant modifications. My work has primarily been to ensure that the project is ready for a release by fixing regressions, migrating the Flutter-based GUI and ensuring that the sample platform (which is used for running regression tests) is stable.

## Sample Platform Improvements
Prior to my involvement with CCExtractor during GSoC I noticed that the [sample platform](https://github.com/CCExtractor/sample-platform) was not working as expected with all the tests ending in failure. Over the course of GSoC, I've made around 12 [PRs](https://github.com/CCExtractor/sample-platform/issues?q=is%3Apr%20author%3Ahrideshmg%20state%3Aclosed) for various fixes and improvements to the platform, here is a brief note on some of them:

- Migrated the platform to Ubuntu 24.04 to fix build failures due to Tesseract
- Optimized Windows VM startup times by around 10 mins through the use of Rclone copy
- Fixed WTV tests timing out due to how Rclone handles chunked reads for large files
- Implemented platform independent tracking for when a regression test last passed on the master branch
- Implemented file caching on Linux and Windows VMs reducing API load and preventing timeouts due to rate limits
- Updated the installation guide for the platform as it was outdated
- Various misc fixes for fixing certain regressions

The sample platform is now fully operational and stable.

## Flutter GUI updates
The [Flutter GUI](https://github.com/CCExtractor/ccextractorfluttergui) had not been updated in a while and hence was refusing to build on the latest Flutter SDK. I [migrated](https://github.com/CCExtractor/ccextractorfluttergui/pull/68) the project and its dependencies to the latest SDK. I also made a [PR](https://github.com/CCExtractor/ccextractorfluttergui/pull/69) to sync the changes in CCextractor with the GUI.

## Week Wise Reports
These reports were written during the GSoC period.
