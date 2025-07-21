# Week 7

## Fixing a sample platform bug
This week, I discovered a bug with the reports that the sample platform leaves on PRs. Apparently, it would group new regressions along with the regressions that are failing on the master branch giving a false impression that the PR does not introduce any new regressions. It has been like this for a long time and it could be the reason why CCextractor currently has a sizeable number of broken regression tests. Fixed it in this [PR](https://github.com/CCExtractor/sample-platform/pull/932). 

Also made a [PR](https://github.com/CCExtractor/sample-platform/pull/933) to separate the tracking of the last passing test for Windows and Linux. Previously, both were grouped together, which made it hard to identify exactly where exactly a regression was introduced.


## CCExtractor regression fixes
Raised three PRs to fix three different regressions for CCextractor. One had to do with how elementary streams were handled, another one was due to a loss of data from conversion of data structures between Rust and C and the third one was simply due to a missing font on the machine which runs the tests.
[PR 1](https://github.com/CCExtractor/ccextractor/pull/1716)
[PR 2](https://github.com/CCExtractor/ccextractor/pull/1714)
[PR 3](https://github.com/CCExtractor/sample-platform/pull/934)
