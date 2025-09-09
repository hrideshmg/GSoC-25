# Week 12
## CCextractor Regressions
In Week 12, I made two PRs to fix a regression under CEA-708 and Options categories respectively. Both of these have since been merged and now all regressions on Linux for CCExtractor have been fixed! ([test run](https://sampleplatform.ccextractor.org/test/6108) on latest commit).

## Sample Platform Improvements
Fixed an issue on the sample platform where certain WTV tests would randomly timeout by implementing file caching for the cloud bucket in this [PR](https://github.com/CCExtractor/sample-platform/pull/938).

## Flutter GUI
Also made two PRs to the Flutter GUI to migrate it to the latest flutter version and sync the changes in the command line arguments.

## List of PRs
[Regression 1](https://github.com/CCExtractor/ccextractor/pull/1733)<br>
[Regression 2](https://github.com/CCExtractor/ccextractor/pull/1732)<br>
[Sample platform fix](https://github.com/CCExtractor/sample-platform/pull/938)<br>
[Flutter GUI upgrade](https://github.com/CCExtractor/ccextractorfluttergui/pull/68)<br>
[Flutter GUI sync](https://github.com/CCExtractor/ccextractorfluttergui/pull/69)<br>
