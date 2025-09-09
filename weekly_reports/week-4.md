# Week 4

## Fix Cargo Tests On Windows 
It was pointed out in the weekly meeting that the cargo tests are currently failing on Window and that this was making Windows based debugging a little more complex. After looking into it, I found that the root issue was with how MSVC and GNU differ in linking behaviours. The latter is more permissive when it comes to external symbols and the lack thereof.

To address this issue I provided mock implementations of the symbols for test runs so that the tests can run without external linkage to C.<br>
[Link To PR](https://github.com/CCExtractor/ccextractor/pull/1704)

## Fix XDS Segmentation faults
The XDS category regression tests have been broken for a while now as the program would abort early due to a segmentation fault (see [here](https://sampleplatform.ccextractor.org/test/5794)). After getting used to Visual Studio and its debugging suite, I isolated the cause to an access request for a particular heap allocated structure. Apparently this structure was not being properly populated in the Rust section of the code.

After comparing it with the C implementation, I made the necessary changes to properly populate this structure and added a few checks to prevent memory leaks. This has fixed all of the errors due to segmentation faults on XDS files as seen [here](https://sampleplatform.ccextractor.org/test/5846).<br>
[Link To PR](https://github.com/CCExtractor/ccextractor/pull/1707)
