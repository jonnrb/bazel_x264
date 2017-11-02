package(default_visibility = ["//visibility:private"])

load("@jonnrb_bazel_asm//rules:nasm_library.bzl", "nasm_library")

BIT_DEPTH = "10"

genrule(
    name = "gen_files",
    srcs = [
        "config.h.in",
    ],
    outs = [
        "x264/config.h",
        "x264/x264_config.h",
    ],
    cmd = "sed 's/$${BIT_DEPTH}/" + BIT_DEPTH + "/g' $(location :config.h.in) > $(location :x264/config.h);" +
          "touch $(location :x264/x264_config.h);" +
          "",
)

CC_COPTS = [
    "-D_GNU_SOURCE",
] + select({
    "@bazel_tools//src:darwin": ["-DSYS_MACOSX=1"],
})

NASM_COPTS = select({
    "@bazel_tools//src:darwin": [
        "-f",
        "macho64",
    ],
}) + [
    "-DARCH_X86_64=1",
    "-DHIGH_BIT_DEPTH=1",
    "-DBIT_DEPTH=" + BIT_DEPTH,
    "-DPIC",
    "-DPREFIX",
    "-DSTACK_ALIGNMENT=16",
]

cc_library(
    name = "x264",
    hdrs = ["x264/x264.h"],
    copts = CC_COPTS,
    strip_include_prefix = "x264",
    visibility = ["//visibility:public"],
    deps = [
        ":headers",
        ":common",
        ":common_x86",
        ":encoder",
    ],
)

cc_library(
    name = "common",
    hdrs = glob([
        "x264/common/*.h",
        "x264/common/x86/*.h",
    ]),
    copts = CC_COPTS,
    srcs = glob([
        "x264/common/*.c",
    ], exclude = [
        "x264/common/opencl.c",
        "x264/common/win32thread.c",
    ]),
    strip_include_prefix = "x264/common",
    deps = [
        ":headers",
        ":common_x86"
    ],
)

cc_library(
    name = "common_x86",
    hdrs = glob([
        "x264/common/x86/*.h",
    ]),
    copts = CC_COPTS,
    srcs = [":asm"] + glob([
        "x264/common/x86/*.c",
    ]),
    strip_include_prefix = "x264/common/x86",
    deps = [":headers"],
)

cc_library(
    name = "encoder",
    hdrs = glob(["x264/encoder/*.h"]) + [
        "x264/encoder/cabac.c",
        "x264/encoder/cavlc.c",
        "x264/encoder/rdo.c",
        "x264/encoder/slicetype.c",
    ],
    copts = CC_COPTS,
    srcs = glob(
        [
            "x264/encoder/*.c",
        ], exclude = [
            "x264/encoder/rdo.c",
            "x264/encoder/slicetype.c",
        ]
    ),
    strip_include_prefix = "x264/encoder",
    deps = [":headers"],
)

cc_library(
    name = "headers",
    hdrs = [
        "x264/x264.h",
        "x264/config.h",
        "x264/x264_config.h",
    ] + glob([
        "x264/common/*.h",
        "x264/common/x86/*.h",
        "x264/encoder/*.h",
    ]),
    strip_include_prefix = "x264",
)

nasm_library(
    name = "asm",
    srcs = glob(
        [
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
