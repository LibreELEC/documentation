# Build Commands Add-ons

The build-system also builds a range of LibreELEC or Kodi binary add-ons. These follow the same basic build command syntax as an image build, e.g. to build `Tvheadend 4.2`

```console
PROJECT=Generic ARCH=x86_64 scripts/create_addon tvheadend42
```

To compile all `game.libretro` add-ons:

```console
PROJECT=Generic ARCH=x86_64 scripts/create_addon game.*
```

To compile all [https://github.com/LibreELEC/LibreELEC.tv/tree/master/packages/mediacenter/kodi-binary-addons](Kodi binary add-ons):

```console
PROJECT=Generic ARCH=x86_64 scripts/create_addon binary
```

To compile all [https://github.com/LibreELEC/LibreELEC.tv/tree/master/packages/addons](LibreELEC binary add-ons):

```console
PROJECT=Generic ARCH=x86_64 scripts/create_addon official
```

To compile all add-ons:

```console
PROJECT=Generic ARCH=x86_64 scripts/create_addon all
```

It is also posssible to exclude specific add-ons:

```console
PROJECT=Generic ARCH=x86_64 scripts/create_addon all -game.* -official -pvr.hts
```

To log errors to the `$BUILD/logs/` directory:

```console
PROJECT=Generic ARCH=x86_64 scripts/create_addon all --write-logs=errors
```

To show help and list all add-on building functions:

```console
PROJECT=Generic ARCH=x86_64 scripts/create_addon --help
```

## Note

In LibreELEC 10.x and newer ARM SoC devices (Allwinner, Amlogic, NXP, Qualcomm, Rockchip, Samsung) use the `ARMv7` or `ARMv8` project. This project uses a minimal distro configuration (disabling most features) to reduce the overall time spent on building add-on dependencies.

```console
PROJECT=ARM ARCH=arm DEVICE=ARMv7 scripts/create_addon all
PROJECT=ARM ARCH=arm DEVICE=ARMv8 scripts/create_addon all
```
