<div align="center">

# owenboringssl

fork of boringssl with `build_testing` forced off for cross-compiles.

<a href="https://count.owenewans.org/owenewans/owenboringssl?theme=moebooru-h&notitle"><img src="https://count.owenewans.org/owenewans/owenboringssl?theme=moebooru-h&notitle" alt="repository views"></a>

`cpp` `tls` `library`

</div>

## features

- BoringSSL, Google's TLS and crypto library, built as `libssl` and
  `libcrypto`
- cross-compiles without configuring `third_party/benchmark`, whose probe
  cannot run a target binary on the build host
- `BUILD_TESTING` cannot be turned back on by a toolchain file, so the Android
  NDK's own reconfigure pass does not reintroduce the failure

## install

Used as the `deps/boringssl` submodule of
[owenboring](https://github.com/owenewans/owenboring), which is where most
consumers pick it up.

Standalone:

```sh
git clone https://github.com/owenewans/owenboringssl
cd owenboringssl
cmake -B build -G Ninja
ninja -C build
```

## usage

The library and its headers are unchanged from upstream:
<https://boringssl.googlesource.com/boringssl>
