# Description:
#   Tulsi, an Xcode project generator for Bazel-bazed projects.

load(
    ":version.bzl",
    "TULSI_VERSION_MINOR",
    "TULSI_VERSIONINFO_LONG",
    "fill_info_plist",
)
load("@build_bazel_rules_apple//apple:versioning.bzl", "apple_bundle_version")
load("@build_bazel_rules_apple//apple:macos.bzl", "macos_application")

package(default_visibility = ["//:__subpackages__"])

licenses(["notice"])  # Apache 2.0

exports_files(["LICENSE"])

fill_info_plist(
    name = "info_plist",
    out = "Info.plist",
    template = "//src/Tulsi:Info.plist",
)

apple_bundle_version(
    name = "AppVersion",
    build_label_pattern = "{project}_{date}_[A-Za-z]*{buildnum}",
    build_version = TULSI_VERSIONINFO_LONG,
    capture_groups = {
        "project": "[^_]*",
        "date": "\\d+",
        "buildnum": "\\d+",
    },
    fallback_build_label = "tulsi_%s_build88" % TULSI_VERSION_MINOR,
)

genrule(
    name = "combine_strings",
    srcs = [
        "//src/Tulsi:en.lproj/Localizable.strings",
        "//src/TulsiGenerator:en.lproj/Localizable.strings",
    ],
    outs = [
        "en.lproj/Localizable.strings",
    ],
    cmd = (
        "cat $(location //src/Tulsi:en.lproj/Localizable.strings) > $(location en.lproj/Localizable.strings); " +
        "echo '\n\n' >> $(location en.lproj/Localizable.strings); " +
        "cat $(location //src/TulsiGenerator:en.lproj/Localizable.strings) >> $(location en.lproj/Localizable.strings); "
    ),
)

filegroup(
    name = "strings",
    srcs = [
        "en.lproj/Localizable.strings",
        "//src/TulsiGenerator:en.lproj/Options.strings",
    ],
)

config_setting(
    name = "is_ci",
    define_values = {
        "is_ci": "1",
    }
)

macos_application(
    name = "tulsi",
    additional_contents = {
        "//src/tools/bazel_cache_reader": "MacOS",
        "//src/tools/module_cache_pruner": "MacOS",
    },
    app_icons = ["//src/Tulsi:Icon"],
    bundle_id = "com.tokopedia.TulsiPlusPlus",
    bundle_name = "Tulsi++",
    provisioning_profile = select({
        ":is_ci": '//src/provisioning_profile:Direct_com.tokopedia.TulsiPlusPlus.provisionprofile',
        "//conditions:default": None,
    }),
    infoplists = [":Info.plist"],
    minimum_os_version = "10.13",
    strings = [":strings"],
    version = ":AppVersion",
    visibility = ["//visibility:public"],
    deps = [
        "//src/Tulsi:tulsi_lib",
    ],
    codesignopts = [
        "--deep",
        "--verbose"
    ]
)
