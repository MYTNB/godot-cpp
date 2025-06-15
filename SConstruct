#!/usr/bin/env python

import os

EnsureSConsVersion(4, 0)
EnsurePythonVersion(3, 8)

try:
    Import("env")
except Exception:
    # Default tools with no platform defaults to gnu toolchain.
    # We apply platform specific toolchains via our custom tools.
    env = Environment(tools=["default"], PLATFORM="")

env.PrependENVPath("PATH", os.getenv("PATH"))

# Custom options and profile flags.
customs = ["custom.py"]
try:
    customs += Import("customs")
except Exception:
    pass
profile = ARGUMENTS.get("profile", "")
if profile:
    if os.path.isfile(profile):
        customs.append(profile)
    elif os.path.isfile(profile + ".py"):
        customs.append(profile + ".py")
opts = Variables(customs, ARGUMENTS)
cpp_tool = Tool("godotcpp", toolpath=["tools"])
cpp_tool.options(opts, env)
opts.Update(env)

Help(opts.GenerateHelpText(env))

excepts = ["debug"]
# Detect and print a warning listing unknown SCons variables to ease troubleshooting.
unknown = opts.UnknownVariables()
unknown =[item for item in unknown.items() if item[0] not in excepts] if unknown else []
if len(unknown) > 0:
    print("WARNING: Unknown SCons variables were passed and will be ignored:")
    for item in unknown:
        print("    " + item[0] + "=" + item[1])

scons_cache_path = os.environ.get("SCONS_CACHE")
if scons_cache_path is not None:
    CacheDir(scons_cache_path)
    Decider("MD5")

cpp_tool.generate(env)
library = env.GodotCPP()

Return("env")
