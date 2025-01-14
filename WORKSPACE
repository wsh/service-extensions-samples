load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

# Release: https://github.com/REPO/releases/tag/YYY
# Get SHA256: `wget https://github.com/$REPO/archive/$COMMIT.tar.gz && sha256sum $COMMIT.tar.gz`
#
# To override with local branches, pass `--override_repository=REPO=/local/path` to Bazel
# or persist the option in `.bazelrc`.

PROXY_WASM_HOST_COMMIT = "5574daf453888e50e7f5ef53e05ad425a0b7fb5c"  # 2023-05-09
PROXY_WASM_HOST_SHA256 = "dd92ac24f3ce58aa15276f90c8a1ee727a867e158dd5bdd03d61cd2eabf21043"

http_archive(
    name = "proxy_wasm_cpp_host",
    sha256 = PROXY_WASM_HOST_SHA256,
    strip_prefix = "proxy-wasm-cpp-host-" + PROXY_WASM_HOST_COMMIT,
    url = "https://github.com/proxy-wasm/proxy-wasm-cpp-host/archive/" + PROXY_WASM_HOST_COMMIT + ".tar.gz",
)

PROXY_WASM_CPP_COMMIT = "921039ae983ce053bf5cba78a85a3c08ff9791e5"  # 2023-05-01
PROXY_WASM_CPP_SHA256 = "a11adfe4e6346d3318ff72643aa5569dc8439d7e8927ed148f93226fa255cc7a"

http_archive(
    name = "proxy_wasm_cpp_sdk",
    sha256 = PROXY_WASM_CPP_SHA256,
    strip_prefix = "proxy-wasm-cpp-sdk-" + PROXY_WASM_CPP_COMMIT,
    url = "https://github.com/proxy-wasm/proxy-wasm-cpp-sdk/archive/" + PROXY_WASM_CPP_COMMIT + ".tar.gz",
)

http_archive(
  name = "com_google_benchmark",
  sha256 = "6bc180a57d23d4d9515519f92b0c83d61b05b5bab188961f36ac7b06b0d9e9ce",
  urls = ["https://github.com/google/benchmark/archive/refs/tags/v1.8.3.tar.gz"],
  strip_prefix = "benchmark-1.8.3",
)

# rules_boost on 2023-06-29, boost @ 1.80.0
http_archive(
    name = "com_github_nelhage_rules_boost",
    url = "https://github.com/nelhage/rules_boost/archive/0598ab9aa992d6ad45088b480e1bf4526ef4ad04.tar.gz",
    strip_prefix = "rules_boost-0598ab9aa992d6ad45088b480e1bf4526ef4ad04",
    sha256 = "1404ffb9f3f7253927c97bc2e05ef6b4a2a5089b76d00cef0f6b7d5a678fad88",
)
load("@com_github_nelhage_rules_boost//:boost/boost.bzl", "boost_deps")
boost_deps()

# Include Google RE2.
http_archive(
    name = "com_google_re2",
    sha256 = "18cf85922e27fad3ed9c96a27733037da445f35eb1a2744c306a37c6d11e95c4",
    strip_prefix = "re2-2023-07-01",
    url = "https://github.com/google/re2/archive/2023-07-01.tar.gz",
)

# Upgrade Abseil to work with latest Emscripten and STANDALONE_WASM
http_archive(
    name = "com_google_absl",  # 2023-04-06T14:42:25Z
    sha256 = "a50452f02402262f9a61a8eedda60f76dda6b9538d36b34b55bce9f74a4d5ef8",
    strip_prefix = "abseil-cpp-e73b9139ee9b853a4bd7812531442c138da09084",
    urls = ["https://github.com/abseil/abseil-cpp/archive/e73b9139ee9b853a4bd7812531442c138da09084.zip"],
)

# Duplicate ProxyWasm WORKSPACE files (dependencies)
# Consider adopting bzlmod for this: https://docs.bazel.build/versions/5.0.0/bzlmod.html

load("@proxy_wasm_cpp_host//bazel:repositories.bzl", "proxy_wasm_cpp_host_repositories")
proxy_wasm_cpp_host_repositories()

load("@proxy_wasm_cpp_host//bazel:dependencies.bzl", "proxy_wasm_cpp_host_dependencies")
proxy_wasm_cpp_host_dependencies()

load("@proxy_wasm_cpp_sdk//bazel:repositories.bzl", "proxy_wasm_cpp_sdk_repositories")
proxy_wasm_cpp_sdk_repositories()

load("@proxy_wasm_cpp_sdk//bazel:dependencies.bzl", "proxy_wasm_cpp_sdk_dependencies")
proxy_wasm_cpp_sdk_dependencies()

load("@proxy_wasm_cpp_sdk//bazel:dependencies_extra.bzl", "proxy_wasm_cpp_sdk_dependencies_extra")
proxy_wasm_cpp_sdk_dependencies_extra()

# Cargo for Bazel via crate_universe:
# http://bazelbuild.github.io/rules_rust/crate_universe.html
load("@rules_rust//crate_universe:repositories.bzl", "crate_universe_dependencies")
crate_universe_dependencies(bootstrap = True)

# Regenerate: $ CARGO_BAZEL_REPIN=1 bazelisk build //...
load("@rules_rust//crate_universe:defs.bzl", "crates_repository")
crates_repository(
    name = "crate_index",
    cargo_lockfile = "//:Cargo.lock",
    generator = "@cargo_bazel_bootstrap//:cargo-bazel",
    lockfile = "//:Cargo.Bazel.lock",
    manifests = ["//:Cargo.toml"],
    supported_platform_triples = ["wasm32-wasi"],
)

# Reference: @crate_index//:proxy-wasm (etc)
load("@crate_index//:defs.bzl", "crate_repositories")
crate_repositories()
