# Week 13
## Fixing the last few windows regressions
In Week 13, I fixed the last remaining Windows regressions by adding the path to the Windows TESSDATA directory in this [PR](https://github.com/CCExtractor/ccextractor/pull/1740) as well as enabling file caching for Rclone in the sample platform [here](https://github.com/CCExtractor/sample-platform/pull/939).

## Fixing Hardsubx
Deepnarayan also pointed out that OCR was broken on the master branch. OCR is actually working, but it was hardsubx (burned in subtitles) that was broken, made a [PR](https://github.com/CCExtractor/ccextractor/pull/1741) to fix that as well. 
Seems like there are no test cases for hardsubx on the sample platform, so I'm also planning to create a few files with burned in subtitles for adding to the platform this week. 

Also implemented some suggested changes to the Flutter GUI PR.
