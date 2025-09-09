# Week 5
## Fixing General and XDS regressions
Few files under the General and XDS category have been failing on Linux citing an exit code of 134. These seemed to be caused by the below error:
```
thread '<unnamed>' panicked at src/decoder/service_decoder.rs:904:43:
called `Result::unwrap()` on an `Err` value: "Too many open files (os error 24)"
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
fatal runtime error: failed to initiate panic, error 5
```
After looking into it, i realized that this was because the file descriptor was getting lost between subsequent function calls resulting in the program opening the same file again and again. After going deeper into it and stepping through the code flow using GDB I realized that the loss of data occurs at the boundary between C and Rust. A recent commit meant to solve unit tests on a newer version of Rust had introduced this subtle bug.
