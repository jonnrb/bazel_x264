package(default_visibility = ["//visibility:private"])

X264_VERSION = "152"

cc_library(
    name = "x264",
    hdrs = [
        "x264.h",
        "x264_config.h",
    ],
    srcs = [
        "libx264.so." + X264_VERSION,
        "libx264.pic.a",
    ],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "x264_isystem",
    hdrs = ["include/x264.h"],
    includes = ["include"],
    visibility = ["//visibility:public"],
)

genrule(
    name = "isystem_x264_h",
    srcs = ["x264.h"],
    outs = ["include/x264.h"],
    cmd = "cp $< $@",
)

genrule(
    name = "make",
    srcs = glob(["x264/**"]),
    outs = [
        "build.log",
        "libx264.so." + X264_VERSION,
        "libx264.pic.a",
        "x264.h",
        "x264_config.h",
    ],
    cmd = "$(location x264/configure) --disable-cli --enable-pic" +
          "  --enable-shared --enable-static --bit-depth=10" +
          "  > $(location build.log);" +
          "make > $(location build.log);" +
          "mv libx264.so." + X264_VERSION +
          "  $(location libx264.so." + X264_VERSION + ");" +
          "mv libx264.a $(location libx264.pic.a);" +
          "cp $(location x264/x264.h) $(location x264.h);" +
          "cp x264_config.h $(location x264_config.h);" +
          "",
)
