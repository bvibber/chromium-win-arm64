# chromium-win-arm64
Experimental dev builds of Chromium for Windows 10 ARM64

## Warning

These developer builds are provided AS-IS for testing convenience only and should NOT be used as your primary browser:

* they are not supported in any way
* they are unstable developer snapshots
* they do not include Google API keys, disabling some services
* I might be building them incorrectly in some way

If you encounter problems with a build, try making your own build against current git and see if you can still reproduce it. If so, report bugs directly to the Chromium project.

## Downloading

See the [releases page on GitHub](https://github.com/brion/chromium-win-arm64/releases) for snapshot downloads built using these instructions.

## Build requirements

Chromium currently builds out of the box for Windows 10 on ARM64, as long as you [follow all the directions](https://chromium.googlesource.com/chromium/src/+/master/docs/windows_build_instructions.md). You should not need to do any custom patching to get a build working, unless there's been a regression.

Requirements:
* a Windows 10 x64 system as build host (you cannot build on the ARM64 device)
    * recommend 16 GiB RAM or more
    * recommend 8 cores or more
* Visual Studio 2019
    * Be sure to install some optional ARM64 components [per instructions](https://chromium.googlesource.com/chromium/src/+/master/docs/windows_build_instructions.md#Setting-up-Windows)
* Unzip the Chromium build "depot_tools" package into C:\src\depot_tools following the above directions.

## Setting up the environment

If you do not wish to modify global environment variables, make a batch file `chromium-dev.bat` like this:

```bat
set PATH=C:\src\depot_tools;%PATH%
set PYTHONPATH=C:\src\depot_tools\third_party
set GYP_MSVS_VERSION=2019
set DEPOT_TOOLS_WIN_TOOLCHAIN=0
```

and run it in a CMD shell before continuing.

## Getting code

Follow the [instructions for git checkout](https://chromium.googlesource.com/chromium/src/+/master/docs/windows_build_instructions.md#Get-the-code).

This may take some time.

## Configuring build

Follow the [instructions for setting up the build](https://chromium.googlesource.com/chromium/src/+/master/docs/windows_build_instructions.md#Setting-up-the-build).

Configure it for ARM64 with jumbo builds for best speed. Run `gn args out/Default` (or whatever the dir name is) and insert these as the args in the editor:

```
# Required or else it defaults to x64
target_cpu = "arm64"

# To get a release-quality build for benchmarking
is_debug = false
is_component_build = false

# Coalesce multiple files and skip the Native Client stuff which won't be used in testing
use_jumbo_build = true
enable_nacl = false
```

You may wish to make other changes to the config.

## Building

Run:

```shell
autoninja -C out\Default chrome
```

This may take some time; on my older 8-core build machine it takes about 3 hours.

The resulting build will appear in `out\Default` (or other specified build dir).

## Extracting the build

The build dir includes a lot of debug info files, intermediate products, and other junk you don't need to distribute.

My current test builds:
* include the `locales` and `resources` subfolders, excluding other subfolders
* exclude `*.pdb` debug files, including everything else in the main folder

This probably includes a bunch of extraneous files and can be cut down further.

## License

See Chromium's license:
https://chromium.googlesource.com/chromium/src/+/master/LICENSE
