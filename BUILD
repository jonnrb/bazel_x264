package(default_visibility = ["//visibility:private"])

load("@jonnrb_bazel_asm//rules:nasm_library.bzl", "nasm_library")

BIT_DEPTH = "10"

config_setting(
    name = "linux",
    values = {"cpu": "k8"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "macos",
    values = {"cpu": "darwin"},
    visibility = ["//visibility:public"],
)

genrule(
    name = "gen_files",
    srcs = [
        "x264_config.h.in",
        "config.h",
    ],
    outs = [
        "x264/config.h",
        "x264/x264_config.h",
    ],
    cmd = "sed 's/$${BIT_DEPTH}/" + BIT_DEPTH + "/g'" +
          "  $(location :x264_config.h.in) > $(location :x264/x264_config.h);" +
          "cp $(location config.h) $(location :x264/config.h);" +
          "",
)

CC_COPTS = [
    "-D_GNU_SOURCE",
] + select({
    ":linux": ["-DSYS_LINUX=1"],
    ":macos": ["-DSYS_MACOSX=1"],
})

NASM_COPTS = [
    "-DARCH_X86_64=1",
    "-DHIGH_BIT_DEPTH=1",
    "-DBIT_DEPTH=" + BIT_DEPTH,
    "-DPIC",
] + select({
    ":linux": [
        "-DSTACK_ALIGNMENT=64",
    ],
    ":macos": [
        "-DPREFIX",
        "-DSTACK_ALIGNMENT=16",
    ],
})

cc_library(
    name = "x264",
    srcs = [":asm"] + glob(
        include = [
            "x264/common/*.h",
            "x264/common/*.c",
            "x264/common/x86/*.h",
            "x264/common/x86/*.c",
            "x264/encoder/*.h",
            "x264/encoder/*.c",
        ],
        exclude = [
            "x264/common/opencl.c",
            "x264/common/win32thread.c",
            "x264/encoder/rdo.c",
            "x264/encoder/slicetype.c",
        ],
    ) + [
        "x264/config.h",
        "x264/x264_config.h",
    ],
    hdrs = ["x264/x264.h"],
    copts = CC_COPTS + [
        "-I$(GENDIR)/x264",
        "-Ix264",
        "-Ix264/common",
        "-Ix264/encoder",
        "-I$(GENDIR)/external/jonnrb_bazel_x264/x264",
        "-Iexternal/jonnrb_bazel_x264/x264",
        "-Iexternal/jonnrb_bazel_x264/x264/common",
        "-Iexternal/jonnrb_bazel_x264/x264/encoder",
    ],
    linkstatic = 1,
    strip_include_prefix = "x264",
    textual_hdrs = [
        "x264/encoder/cabac.c",
        "x264/encoder/cavlc.c",
        "x264/encoder/rdo.c",
        "x264/encoder/slicetype.c",
    ],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "x264_isystem",
    includes = ["x264"],
    visibility = ["//visibility:public"],
    deps = [":x264"],
)

nasm_library(
    name = "asm",
    srcs = glob(
        include = [
            "x264/common/x86/*.asm",
        ],
        exclude = [
            "x264/common/x86/dct-64.asm",
            "x264/common/x86/intrapred8.asm",
            "x264/common/x86/intrapred8_allangs.asm",
            "x264/common/x86/ipfilter8.asm",
            "x264/common/x86/pixel-32.asm",
            "x264/common/x86/sad-a.asm",
        ],
    ),
    copts = NASM_COPTS,
)
