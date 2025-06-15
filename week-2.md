# Week 2 Report
## GPAC Going Down
During the start of the week, the upstream GPAC servers went down which prevented the windows builds from succeeding. To work around this issue, i got a binary from the wayback machine and hosted it on a GitHub release to continue testing my changes.
 ```
- name: Download and install GPAC
        run: |
          $gpacInstaller = "gpac-2.4-rev0-g5d70253a-master-x64.exe"
          Invoke-WebRequest -Uri "https://github.com/hrideshmg/ccextractor/releases/download/temp/$gpacInstaller" -OutFile $gpacInstaller
          Start-Process -FilePath ".\$gpacInstaller" -ArgumentList "/S" -Wait
          echo "C:\Program Files\GPAC" | Out-File -Append -Encoding ascii $env:GITHUB_PATH
```
The servers came online soon after though, so this wasn't needed for long.
## Sample Platform Fixed!
Thanks to [PR #930](https://github.com/CCExtractor/sample-platform/pull/930), the Windows tests are finally working again after two weeks of debugging with Rclone and isolating the issue using a Windows VM.
This means that the sample platform is fully operational again, and can be relied upon to help fix regressions and prevent new ones from popping up.

## Fixing WTV and XDS regressions
As evidenced by this [test](https://sampleplatform.ccextractor.org/test/5794), a lot of XDS tests are currently failing on Windows due to segmentation faults. The cause for this seems to be in the rust section of the code, as a C-only build seems to run fine. Currently looking into this and getting familiar with Visual Studio's debugging suite.
