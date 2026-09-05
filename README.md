# BoringSSL

Fork of [google/boringssl](https://github.com/google/boringssl), pinned as
the `deps/boringssl` submodule of
[owenboring](https://github.com/owenewans/owenboring) (itself a fork of
[cloudflare/boring](https://github.com/cloudflare/boring)), maintained for
[snolc](https://github.com/owenewans/snolc). Default branch is
[`android-build-testing-off`](https://github.com/owenewans/owenboringssl/tree/android-build-testing-off),
based on upstream commit
[`e2a57cfb4`](https://github.com/google/boringssl/commit/e2a57cfb4d915b4ba820585aef9fdee7bca13fe5);
everything outside the one change below is unmodified upstream BoringSSL.

The one change: [`CMakeLists.txt`](CMakeLists.txt) hard-sets
`set(BUILD_TESTING OFF CACHE BOOL "" FORCE)` immediately after
`include(CTest)`. `BUILD_TESTING` (the standard CTest option, defaulting
`ON`) controls whether this top-level `CMakeLists.txt` adds the
`third_party/benchmark` subdirectory. That subdirectory's own configure-time
regex-backend probe is a `try_compile` + `try_run` check, which cannot
execute a cross-compiled target binary on the build host and always fails:

```
CMake Error at third_party/benchmark/CMakeLists.txt:325 (message):
  Failed to determine the source files for the regular expression backend
```

when cross-compiling (Android in particular, via `boring-sys`). Passing
`-DBUILD_TESTING=OFF` from the outside (e.g. from `boring-sys`'s own build
script) reaches the actual `cmake` invocation, but the Android NDK's
`android.toolchain.cmake` performs its own internal cache-reset/reconfigure
pass that does not reliably carry every externally-passed `-D` flag through
to where `if(BUILD_TESTING)` is evaluated. Forcing it directly in this file,
as a `CACHE ... FORCE` write, is authoritative regardless of that.

Consumers building BoringSSL as a library dependency only ever need
`libssl`/`libcrypto`; BoringSSL's own unit tests and benchmarks are never
invoked as part of a normal build, so this has no effect on the produced
library.

BoringSSL is a fork of OpenSSL that is designed to meet Google's needs.

Although BoringSSL is an open source project, it is not intended for general
use, as OpenSSL is. We don't recommend that third parties depend upon it. Doing
so is likely to be frustrating because there are no guarantees of API or ABI
stability.

Programs ship their own copies of BoringSSL when they use it and we update
everything as needed when deciding to make API changes. This allows us to
mostly avoid compromises in the name of compatibility. It works for us, but it
may not work for you.

BoringSSL arose because Google used OpenSSL for many years in various ways and,
over time, built up a large number of patches that were maintained while
tracking upstream OpenSSL. As Google's product portfolio became more complex,
more copies of OpenSSL sprung up and the effort involved in maintaining all
these patches in multiple places was growing steadily.

Currently BoringSSL is the SSL library in Chrome/Chromium, Android (but it's
not part of the NDK) and a number of other apps/programs.

Project links:

  * [API documentation](https://commondatastorage.googleapis.com/chromium-boringssl-docs/headers.html)
  * [Issue tracker](https://crbug.com/boringssl)
    * [Filing new (public) issues](https://crbug.com/boringssl/new)
  * [CI](https://ci.chromium.org/p/boringssl/g/main/console)
  * [Code review](https://boringssl-review.googlesource.com)
  * [Security Advisories](./docs/advisories/)

To file a security issue, use the [Chromium process](https://www.chromium.org/Home/chromium-security/reporting-security-bugs/) and mention in the report this is for BoringSSL. You can ignore the parts of the process that are specific to Chromium/Chrome.

There are other files in this directory which might be helpful:

  * [PORTING.md](./PORTING.md): how to port OpenSSL-using code to BoringSSL.
  * [BUILDING.md](./BUILDING.md): how to build BoringSSL
  * [INCORPORATING.md](./INCORPORATING.md): how to incorporate BoringSSL into a project.
  * [API-CONVENTIONS.md](./API-CONVENTIONS.md): general API conventions for BoringSSL consumers and developers.
  * [STYLE.md](./STYLE.md): rules and guidelines for coding style.
  * include/openssl: public headers with API documentation in comments. Also [available online](https://commondatastorage.googleapis.com/chromium-boringssl-docs/headers.html).
  * [FUZZING.md](./FUZZING.md): information about fuzzing BoringSSL.
  * [CONTRIBUTING.md](./CONTRIBUTING.md): how to contribute to BoringSSL.
  * [BREAKING-CHANGES.md](./BREAKING-CHANGES.md): notes on potentially-breaking changes.
  * [SANDBOXING.md](./SANDBOXING.md): notes on using BoringSSL in a sandboxed environment.
