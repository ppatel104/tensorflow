# Description:
#  Tools for building the TensorFlow pip package.

package(default_visibility = ["//visibility:private"])

load("//tensorflow:tensorflow.bzl", "transitive_hdrs")
load("//third_party/mkl:build_defs.bzl", "if_mkl")
load("//tensorflow/core:platform/default/build_config_root.bzl", "tf_additional_license_deps")

# This returns a list of headers of all public header libraries (e.g.,
# framework, lib), and all of the transitive dependencies of those
# public headers.  Not all of the headers returned by the filegroup
# are public (e.g., internal headers that are included by public
# headers), but the internal headers need to be packaged in the
# pip_package for the public headers to be properly included.
#
# Public headers are therefore defined by those that are both:
#
# 1) "publicly visible" as defined by bazel
# 2) Have documentation.
#
# This matches the policy of "public" for our python API.
transitive_hdrs(
    name = "included_headers",
    deps = [
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core:stream_executor",
        "//third_party/eigen3",
    ],
)

py_binary(
    name = "simple_console",
    srcs = ["simple_console.py"],
    srcs_version = "PY2AND3",
    deps = ["//tensorflow:tensorflow_py"],
)

# On Windows, python binary is a zip file of runfiles tree.
# Add everything to its data dependency for generating a runfiles tree
# for building the pip package on Windows.
py_binary(
    name = "simple_console_for_windows",
    srcs = ["simple_console_for_windows.py"],
    data = [
        "MANIFEST.in",
        "README",
        "setup.py",
        ":included_headers",
        "//tensorflow:tensorflow_py",
        "//tensorflow/contrib/ndlstm:all_files",
        "//tensorflow/contrib/nn:all_files",
        "//tensorflow/contrib/session_bundle:all_files",
        "//tensorflow/contrib/slim:all_files",
        "//tensorflow/contrib/slim/python/slim/data:all_files",
        "//tensorflow/contrib/slim/python/slim/nets:all_files",
        "//tensorflow/contrib/specs:all_files",
        "//tensorflow/contrib/tensor_forest:all_files",
        "//tensorflow/contrib/tensor_forest/hybrid:all_files",
        "//tensorflow/python:util_example_parser_configuration",
        "//tensorflow/python/debug:all_files",
        "//tensorflow/python/saved_model:all_files",
        "//tensorflow/python/tools:all_files",
        # The following target has an issue when archiving them into the python
        # zip, exclude them for now.
        # "//tensorflow/tensorboard",
        # This package does not build. Exclude it in windows for now.
        # "//tensorflow/examples/tutorials/mnist:package",
    ],
    srcs_version = "PY2AND3",
    deps = ["//tensorflow:tensorflow_py"],
)

filegroup(
    name = "licenses",
    data = [
        "//third_party/eigen3:LICENSE",
        "//third_party/hadoop:LICENSE.txt",
        "@boringssl//:LICENSE",
        "@com_googlesource_code_re2//:LICENSE",
        "@curl//:COPYING",
        "@eigen_archive//:COPYING.MPL2",
        "@farmhash_archive//:COPYING",
        "@gemmlowp//:LICENSE",
        "@gif_archive//:COPYING",
        "@grpc//:LICENSE",
        "@highwayhash//:LICENSE",
        "@jemalloc//:COPYING",
        "@jpeg//:LICENSE.md",
        "@libxsmm_archive//:LICENSE",
        "@local_config_sycl//sycl:LICENSE.text",
        "@nanopb_git//:LICENSE.txt",
        "@nccl_archive//:LICENSE.txt",
        "@png_archive//:LICENSE",
        "@protobuf//:LICENSE",
        "@six_archive//:LICENSE",
        "@org_pocoo_werkzeug//:LICENSE",
        "@zlib_archive//:zlib.h",
    ] + tf_additional_license_deps(),
)

sh_binary(
    name = "build_pip_package",
    srcs = ["build_pip_package.sh"],
    data = select({
        "//tensorflow:windows": [":simple_console_for_windows"],
        "//conditions:default": [
            ":licenses",
            "MANIFEST.in",
            "README",
            "setup.py",
            ":included_headers",
            ":simple_console",
            "//tensorflow:tensorflow_py",
            "//tensorflow/contrib/labeled_tensor:all_files",
            "//tensorflow/contrib/ndlstm:all_files",
            "//tensorflow/contrib/nn:all_files",
            "//tensorflow/contrib/session_bundle:all_files",
            "//tensorflow/contrib/slim:all_files",
            "//tensorflow/contrib/slim/python/slim/data:all_files",
            "//tensorflow/contrib/slim/python/slim/nets:all_files",
            "//tensorflow/contrib/specs:all_files",
            "//tensorflow/contrib/tensor_forest:all_files",
            "//tensorflow/contrib/tensor_forest/hybrid:all_files",
            "//tensorflow/examples/tutorials/mnist:package",
            "//tensorflow/python:util_example_parser_configuration",
            "//tensorflow/python/debug:all_files",
            "//tensorflow/python/saved_model:all_files",
            "//tensorflow/python/tools:all_files",
            "//tensorflow/tensorboard",
        ],
    }) + if_mkl(["//third_party/mkl:intel_binary_blob"]),
)
