# Building \(Advanced\)

## Build-System Options

The following options can be used in LE 10.x+ to customise the build experience:

| Name | Values | Default | Description |
| :--- | :--- | :--- | :--- |
| `THREADCOUNT` | `#`,`#%` | `100%` | Maximum number of packages to build concurrently. On an 8-core system, `50%` would build up to 4 packages at a time. |
| `ONELOG` | `yes`,`no` | `no` | If `yes`, packages will not create individual logs in `${THREAD_CONTROL}/logs`. If `THREADCOUNT` is 1 the default becomes `ONELOG=yes` in which case use `ONELOG=no` to ensure individual logs are created for each package. |
| `LOGCOMBINE` | `always`,`never`,`fail` | `always` | Determine under what circumstances package logs are written to `stdout`. Using `never` and `fail` can slightly reduce IO. |
| `MTBOOKENDS` | `yes`,`no` | `yes` | Adds `<<<` and `>>>` tags to the combined log output \(to facilitate searching\). |
| `DISABLE_COLORS` | `yes`,`no` | `no` | Control whether build system output is colored or not. |
| `MTCOLORS` | `always`,`never`,`auto` | `auto` | If `DISABLE_COLORS=yes` this option controls how `scripts/pkgbuilder.py` progress and status information is colored. `auto` will disable colors when output is being redirected to a file \(ie. not a terminal\). |
| `MTVERBOSE` | `yes`,`no` | `no` | Output additional job state information to `stderr` during the build. |
| `MTPROGRESS` | `yes`,`no` | `no` | Output real-time load, memory and in-progress job information to `stderr` at 1-second intervals |
| `MTDEBUG` | `yes`,`no` | `no` | Output detailed debug information to `${THREAD_CONTROL}/debug.log` |
| `MTADDONBUILD` | `yes`,`no` | `no` | If `no`, the build will end after the first failure. When building add-ons, we typically ignore individual package failures and continue building until all packages have been built \(or failed\). |
| `MTIMMEDIATE` | `yes`,`no` | `no` | When `MTADDONBUILD=no` and a package fails, the build can finish after all currently building packages have completed, or it can terminate those packages immediately. Allowing packages that are currently building to finish building can save time when restarting the build. |
| `MTINTERVAL` | `#` | `60` | System load information is captured at regular intervals in `${THREAD_CONTROL}/loadstats`. |
| `AUTOREMOVE` | `yes`,`no` | `no` | Remove source code directories during the build once the source code directory is not required. |

## Persistent Options

Options can be specified on the command line, or added to persistent "options" files located under the root folder of the buildsystem, e.g. `~/LibreELEC.tv/.libreelec/options` or the users home folder, e.g. `~/.libreelec/options` - if both files exist, home folder options trump root folder options. The following gives a fairly clean and comprehensive output while still allowing command-line overrides:

```text
LOGCOMBINE=${LOGCOMBINE:-never}
MTPROGRESS=${MTPROGRESS:-yes}
MTIMMEDIATE=${MTIMMEDIATE:-no}
```

## Builder Name and Version

If you are building and sharing images via the LibreELEC forums it is good to include a custom "builder" name with your images. This allows the team to count the number of active installs using your images \(we can share this info on request\). Builder name also populates into `/etc/os-release` and logfiles, which helps forum staff supporting users differentiate between official releases and community created images. It is also possible to set custom versioning. e.g.

```text
PROJECT=Generic ARCH=x86_64 BUILDER_NAME=yourname BUILDER_VERSION=123 make image
```

It is also possible to set persistent `BUILDER_NAME` and `BUILDER_VERSION` in "options" files.

## Debug Images

The build-system automatically strips debug symbols from most packages. To disable this and include debug symbols and common debugging packages in the image, enable `DEBUG`, e.g.

```text
PROJECT=Generic ARCH=x86_64 DEBUG=yes make image
```

To also include valgrind:

```text
PROJECT=Generic ARCH=x86_64 DEBUG=yes VALGRIND=yes make image
```

Note: The resulting image will be considerably larger than normal. The default boot partition is 512MB, but debug images may exceed this and require a 1GB partition.

