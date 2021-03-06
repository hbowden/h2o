# Fuzzing

This directory contains code and test data for fuzz testing h2o with LLVM's [LibFuzzer](http://libfuzzer.info). 

## Building the test drivers

To build the fuzz test drivers, pass `-DBUILD_FUZZER=ON` to `cmake`. This this will instrument `libh2o`, and build fuzz test driver programs for both HTTP/1 and HTTP/2: `h2o-fuzzer-http1` and
  `h2o-fuzzer-http2`, respectively. Note that you must have a version of LLVM Clang installed that supports LibFuzzer in order for this build to succeed.

## Test corpus information

The test corpuses included in this were initially created by first patching h2o with the included `fuzz/gather-data.patch` and then running the unit tests. The corpuses were then broadened via fuzzing with this fuzzer and minimized. See the [LibFuzzer docs](http://llvm.org/docs/LibFuzzer.html) for more information on how minimization works.

## Running the fuzzers

You will likely want to tailor fuzzer options to your execution environment, but here are basic examples of running each fuzzer:

HTTP/1: `ASAN_OPTIONS=detect_leaks=0 ./h2o-fuzzer-http1 -max_len=$((16 * 1024 )) fuzz/http1-corpus`

HTTP/2: `ASAN_OPTIONS=detect_leaks=0 ./h2o-fuzzer-http2 -max_len=$((16 * 1024 )) fuzz/http2-corpus`

The fuzzer looks at `H2O_FUZZER_CLIENT_TIMEOUT` in order to configure the
client thread event loop timeout in milli-seconds. It defaults to 10 ms.
