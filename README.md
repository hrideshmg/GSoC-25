# Google Summer Of Code 2025 - Final Report
## CCExtractor v1.00 Release
Organization: [CCextractor](https://summerofcode.withgoogle.com/programs/2025/organizations/ccextractor-development)<br>
Project: [1.00 Release](https://summerofcode.withgoogle.com/programs/2025/projects/t4gOHYbv)<br>
Contributor: Hridesh MG<a href="https://www.linkedin.com/in/hridesh-mg/">
  <img src="https://upload.wikimedia.org/wikipedia/commons/8/81/LinkedIn_icon.svg" width="20" height="20" align="top"/>
</a>

---

## Summary
CCExtractor’s last stable release (v0.94) came out in 2021, and since then, the codebase has undergone significant modifications. My work has primarily been to ensure that the project is ready for the 1.00 release by fixing regressions, updating the Flutter-based GUI and ensuring that the sample platform is stable.

## Sample Platform Improvements
Prior to my involvement with CCExtractor during GSoC, I noticed that the [sample platform](https://github.com/CCExtractor/sample-platform) was not working with all the tests ending in failure. Over the course of GSoC, I've made around 12 [PRs](#sample-platform) for various fixes and improvements to the platform, here is a brief note on some of the changes:

- Migrated the platform to Ubuntu 24.04 to fix test failures due to tesseract being out of date
- Optimized Windows VM startup times by around 10 mins through the use of Rclone copy
- Fixed WTV tests timing out due to how Rclone handles chunked reads for large files
- Implemented platform independent tracking for when a regression test last passed on the master branch
- Implemented file caching on Linux and Windows VMs reducing API load and preventing timeouts due to rate limits
- Updated the installation guide for the platform
- Various misc fixes for fixing certain regressions

As a result of these changes, the sample platform is now fully operational and stable. I've also added a few new regressions for testing certain file types/parameters that were not previously being tested.

#### A Note On A Challenging Fix
One issue that I would like to highlight was the timing out of certain WTV/XDS files on Windows VMs. Puzzlingly, these files would run perfectly fine locally but the moment they ran in the VM provisioned by the sample platform, the tests would start to time out, eventually causing the entire test suite to fail due to the whole process taking forever.

This issue took me a week or two to fix due to how hard it was to reproduce. Eventually, I had isolated it down to Rclone, which is the program that we use for mounting the cloud buckets that contain the sample files. After experimenting with a whole bunch of configuration options for the program, I came upon this [forum post](https://forum.rclone.org/t/vfs-is-slow-when-reading-in-small-8-bytes-chunks/40161) which detailed a similar issue with file reads from large files.<br>
After reading some obscure [GitHub conversations](https://github.com/winfsp/winfsp/issues/307) on the file system emulator that Rclone uses under the hood (WinFSP), I realized that the issue was with how chunked reads were being handled by the Windows kernel. Enabling kernel file metadata caching by setting `FileInfoTimeout=-1` finally solved the issue.

## CCextractor Regression Fixes
This is where the bulk of my work during GSoC has been focused. After fixing the Sample Platform, there were around 51 [broken](https://sampleplatform.ccextractor.org/test/5794) regressions on Windows and Linux. Note that a lot of these were caused by issues with the sample platform itself, there were only around 10-15 regressions that were failing due to bugs in CCextractor itself.

These bugs were varied in nature, ranging from memory management issues like segmentation faults in the CEA-708 and XDS decoders, to logical errors in Unicode character encoding and incorrect luminance calculations for OCR. A significant portion of the work involved using debugging tools like GDB to trace these issues across the C and Rust codebases.

A major contributor to the accumulation of the bugs in CCextractor was that the sample platform, when reporting on new PRs, would group new failing test cases with old ones already present in the master branch. This gave the false illusion that the PR was free from bugs. This was apparently a bug that had existed for more than 2 years and was fixed in my PR [here](https://github.com/CCExtractor/sample-platform/pull/932).


### Hardsubx Debugging
One particular interesting issue that I had a lot of fun debugging were the Hardsubx discrepancies between the C and Rust builds of CCextractor. After fixing the initial segmentation fault for Hardsubx on Rust, the generated SRT files were often inferior to the C only builds, which was puzzling because the Rust code uses largely the same logic as C.

After skimming through the code, I went a bit deeper and wrote (with the help of AI) a custom [script](https://gist.github.com/hrideshmg/bb944c7665e3382e1620c4404f479529) using OpenCV that would display image files on a grid and update them as they were modified live. Using this script, I kept an eye on the pre-processing stages for the image files from the video and noticed slight differences in the luminance threshold stage between Rust and C.

After looking into the implementation for luminance calculation, I noticed that in the old C code, the RGB to LAB conversion was done [manually](https://github.com/hrideshmg/ccextractor/blob/ocr_fixes/src/lib_ccx/hardsubx_imgops.c#L69), in Rust we instead [use](https://github.com/hrideshmg/ccextractor/blob/master/src/rust/src/hardsubx/imgops.rs#L15) the `palette` crate for this purpose. 
The `Srgb` implementation from `palette` however assumes that the input image is [gamma corrected](https://en.wikipedia.org/wiki/Gamma_correction), and thus performs gamma decoding to make the RGB values linear.

This was, however unnecessary, because our input is already linear RGB. I modified the code to use [`LinSrgb`](https://docs.rs/palette/latest/palette/type.LinSrgb.html) instead and that fixed the issue.
<hr>

The details of the other fixes are available in their respective PR descriptions, and more of the debugging strategies I followed are detailed in the weekly reports linked [here](#week-wise-reports).

## Flutter GUI updates
The [Flutter GUI](https://github.com/CCExtractor/ccextractorfluttergui) had not been updated in a while and hence was refusing to build on the latest Flutter SDK. I [migrated](https://github.com/CCExtractor/ccextractorfluttergui/pull/68) the project and its dependencies to the latest SDK and also made two PRs to sync the changes in the latest CCextractor builds with the GUI.

## Week Wise Reports
These reports were written during the GSoC period.
| Week | Report Link |
|------|-------------|
| 1    | [Week 1](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-1.md) |
| 2    | [Week 2](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-2.md) |
| 3    | [Week 3](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-3.md) |
| 4    | [Week 4](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-4.md) |
| 5    | [Week 5](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-5.md) |
| 6    | [Week 6](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-6.md) |
| 7    | [Week 7](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-7.md) |
| 10   | [Week 10](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-10.md) |
| 11   | [Week 11](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-11.md) |
| 12   | [Week 12](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-12.md) |
| 13   | [Week 13](https://github.com/hrideshmg/GSoC-25/blob/main/weekly_reports/week-13.md) |

Note: I had my finals between Weeks 8-10, hence I was unable to work much. This was the primary reason why i extended my project timeline to 14 weeks.

## Pull Requests

### CCextractor
| No. | Pull Request | PR Name                                          | Status |
|-----|--------------|--------------------------------------------------|--------|
| 1   | [#1746](https://github.com/CCExtractor/ccextractor/pull/1746) | fix: ocr luminance calculation fix            | Open |
| 2   | [#1742](https://github.com/CCExtractor/ccextractor/pull/1742) | Fix `--ocrlang` argument on rust              | Merged |
| 3   | [#1741](https://github.com/CCExtractor/ccextractor/pull/1741) | Fix Hardsubx OCR                              | Merged |
| 4   | [#1740](https://github.com/CCExtractor/ccextractor/pull/1740) | Fix DVB Regressions on windows                | Merged |
| 5   | [#1733](https://github.com/CCExtractor/ccextractor/pull/1733) | fix: unicode encoding regression              | Merged |
| 6   | [#1732](https://github.com/CCExtractor/ccextractor/pull/1732) | fix: rust bitstream segfault                  | Merged |
| 7   | [#1729](https://github.com/CCExtractor/ccextractor/pull/1729) | fix: CEA-708 segmentation faults on MP4 files | Merged |
| 8   | [#1722](https://github.com/CCExtractor/ccextractor/pull/1722) | refactor: remove api structures               | Merged |
| 9   | [#1716](https://github.com/CCExtractor/ccextractor/pull/1716) | fix: elementary stream regressions            | Merged |
| 10  | [#1714](https://github.com/CCExtractor/ccextractor/pull/1714) | fix: dvd regressions                          | Merged |
| 11  | [#1707](https://github.com/CCExtractor/ccextractor/pull/1707) | fix: XDS segmentation faults                  | Merged |
| 12  | [#1705](https://github.com/CCExtractor/ccextractor/pull/1705) | fix: trigger windows builds in PRs            | Merged |
### Sample Platform
| No. | Pull Request | PR Name                                            | Status |
|-----|--------------|----------------------------------------------------|--------|
| 1   | [#939](https://github.com/CCExtractor/sample-platform/pull/939) | Rclone optimizations + tesseract fixes       | Merged |
| 2   | [#938](https://github.com/CCExtractor/sample-platform/pull/938) | Enable file caching in GCSFuse to fix WTV timeouts | Merged |
| 3   | [#936](https://github.com/CCExtractor/sample-platform/pull/936) | fix: PR comments being rendered as code blocks | Merged |
| 4   | [#935](https://github.com/CCExtractor/sample-platform/pull/935) | chore: add migration for last_passed_on      | Merged |
| 5   | [#934](https://github.com/CCExtractor/sample-platform/pull/934) | fix: add missing font                        | Merged |
| 6   | [#933](https://github.com/CCExtractor/sample-platform/pull/933) | feat: add per platform last passed tracking  | Merged |
| 7   | [#932](https://github.com/CCExtractor/sample-platform/pull/932) | fix: test comparison                         | Merged |
| 8   | [#930](https://github.com/CCExtractor/sample-platform/pull/930) | fix: windows tests getting stuck and timing out | Merged |
| 9   | [#929](https://github.com/CCExtractor/sample-platform/pull/929) | perf: optimize startup time by using rclone copy directly | Merged |
| 10  | [#928](https://github.com/CCExtractor/sample-platform/pull/928) | fix: SQLAlchemy subquery and cache warnings  | Merged |
| 11  | [#926](https://github.com/CCExtractor/sample-platform/pull/926) | [Improvement] Fix first time install errors and update docs | Merged |
| 12  | [#925](https://github.com/CCExtractor/sample-platform/pull/925) | [Improvement] migrate to Ubuntu 24.04        | Merged |
### Flutter GUI
| No. | Pull Request | PR Name                                            | Status |
|-----|--------------|----------------------------------------------------|--------|
| 1   | [#70](https://github.com/CCExtractor/ccextractorfluttergui/pull/70) | fix: migrate navigation rail to built in package | Open |
| 2   | [#69](https://github.com/CCExtractor/ccextractorfluttergui/pull/69) | Update CCExtractor args to latest format and bundle latest build | Open |
| 3   | [#68](https://github.com/CCExtractor/ccextractorfluttergui/pull/68) | Bump Flutter SDK to latest version | Open |

## Outcomes
- v1.00 is now release-ready
- All major regressions reported by the Sample Platform have been addressed
- Sample Platform is now stable and ready for testing future PRs
- The Flutter GUI is no longer deprecated and works with the latest SDK

## Acknowledgements
Working on this project has been quite an enriching experience. I got to work with a wide variety of tools and platforms like GDB, Flutter, GCP, Rust, C etc and I've definitely learnt a lot from working across all of these domains.

I'm truly thankful to my mentors Prateek Sunal and Willem Van Iseghem, our org-admin Carlos Fernandez Sanz, and my colleague Deepnarayan Sett for their guidance and support along the way.
