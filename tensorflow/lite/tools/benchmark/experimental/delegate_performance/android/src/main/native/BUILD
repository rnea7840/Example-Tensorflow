# Description:
#  Holds the native layer of the app.

load("//tensorflow/lite:build_def.bzl", "tflite_jni_binary")
load("//tensorflow:tensorflow.bzl", "clean_dep")
load("//tensorflow/lite/tools/benchmark/experimental/delegate_performance/android:build_defs.bzl", "accuracy_benchmark_extra_deps", "latency_benchmark_extra_deps")

package(
    # copybara:uncomment default_applicable_licenses = ["//tensorflow:license"],
    default_visibility = ["//tensorflow/lite/tools/benchmark/experimental/delegate_performance/android:__subpackages__"],
    licenses = ["notice"],
)

tflite_jni_binary(
    name = "libdelegate_performance_benchmark.so",
    srcs = ["delegate_performance_benchmark_jni.cc"],
    deps = [
        ":accuracy_benchmark",
        ":latency_benchmark",
        "//tensorflow/lite/acceleration/configuration:configuration_fbs",
        "//tensorflow/lite/delegates/utils/experimental/stable_delegate:tflite_settings_json_parser",
        "//tensorflow/lite/java/jni",
        "//tensorflow/lite/tools/benchmark/experimental/delegate_performance/android/proto:delegate_performance_cc_proto",
        "@flatbuffers",
    ],
)

cc_library(
    name = "status_codes",
    hdrs = ["status_codes.h"],
)

cc_library(
    name = "latency_benchmark",
    srcs = ["latency_benchmark.cc"],
    hdrs = ["latency_benchmark.h"],
    deps = [
        "//tensorflow/core/util:stats_calculator_portable",
        "//tensorflow/lite:minimal_logging",
        "//tensorflow/lite/acceleration/configuration:configuration_fbs",
        "//tensorflow/lite/profiling:memory_info",
        "//tensorflow/lite/tools/benchmark:benchmark_tflite_model_lib",
        "//tensorflow/lite/tools/benchmark/experimental/delegate_performance/android/proto:delegate_performance_cc_proto",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:str_format",
    ] + latency_benchmark_extra_deps(),
)

cc_library(
    name = "accuracy_benchmark",
    srcs = ["accuracy_benchmark.cc"],
    hdrs = ["accuracy_benchmark.h"],
    deps = [
        ":status_codes",
        "//tensorflow/lite:minimal_logging",
        "//tensorflow/lite/acceleration/configuration:configuration_fbs",
        "//tensorflow/lite/acceleration/configuration:stable_delegate_plugin",
        "//tensorflow/lite/acceleration/configuration:xnnpack_plugin",
        "//tensorflow/lite/core/acceleration/configuration:nnapi_plugin",
        "//tensorflow/lite/delegates/utils/experimental/stable_delegate:tflite_settings_json_parser",
        "//tensorflow/lite/experimental/acceleration/mini_benchmark:status_codes",
        "//tensorflow/lite/experimental/acceleration/mini_benchmark/c:c_api",
        "//tensorflow/lite/kernels/internal:compatibility",
        "//tensorflow/lite/tools:command_line_flags",
        "//tensorflow/lite/tools:tool_params",
        "@flatbuffers",
    ] + select({
        # On Android, as the validation runs in a separate process as a
        # different binary, any TFLite delegates to be validated need to
        # include corresponding delegate plugins.
        clean_dep("//tensorflow:android"): [
            "//tensorflow/lite/acceleration/configuration:gpu_plugin",
        ],
        "//conditions:default": [],
    }) + accuracy_benchmark_extra_deps(),
)

cc_library(
    name = "benchmark_native",
    srcs = ["libdelegate_performance_benchmark.so"],
)
