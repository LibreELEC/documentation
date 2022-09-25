# Building \(Docker\)

## Build Container

```bash
# apply patches to make the image like in CI
sed -i -e "/^USER docker/i RUN ln -s /usr/bin/gcc-10 /usr/bin/cc" tools/docker/focal/Dockerfile

# per tools/docker/README.md
docker build --pull -t libreelec tools/docker/focal
```

## Run Commands

For a list of commands, see [Build (Basics)](build-basics.md).

### Basic

```bash
docker run \
  --it --rm \
  --log-driver none \
  -v `pwd`:/build -w /build \
  -e PROJECT=Generic \
  -e ARCH=x86_64 \
  -e MTPROGRESS=yes \
  libreelec CMD
```

### Advanced

Limit CPU and RAM so your system remains responsive.

```bash
docker run \
  --it --rm \
  --log-driver none \
  -v `pwd`:/build -w /build \
  `# setting these the same disables swapping` \
  --memory "6g" --memory-swap "6g" \
  `# uses all cpus, but will reserve cycles on each` \
  `#--cpus "4"` \
  `# limit to certain processors` \
  --cpuset-cpus "0-3" \
  -e PROJECT=Generic \
  -e ARCH=x86_64 \
  -e MTPROGRESS=yes \
  libreelec CMD
```

