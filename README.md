# taf-bedtools

TAFFISH wrapper for [BEDTools](https://bedtools.readthedocs.io/), a widely
used command-line toolkit for genome arithmetic on BED, GFF/GTF, VCF, and BAM
files.

This repository packages upstream BEDTools 2.31.1 as a TAFFISH tool app. It
exposes the upstream `bedtools` command through a versioned TAFFISH entry point
while keeping the runtime environment inside a portable Debian 12 container
image.

## Installation

Install from the public TAFFISH Hub index:

```sh
taf update
taf install bedtools
```

Install the exact release:

```sh
taf install bedtools 2.31.1-r1
```

For local testing before the app is published to the public index:

```sh
taf install --from .
```

## Usage

Show TAFFISH app help:

```sh
taf-bedtools --help
```

Show upstream BEDTools help and version:

```sh
taf-bedtools bedtools --help
taf-bedtools bedtools --version
```

Run common BEDTools workflows:

```sh
taf-bedtools bedtools intersect -a query.bed -b targets.bed
taf-bedtools bedtools sort -i intervals.bed > intervals.sorted.bed
taf-bedtools bedtools merge -i intervals.sorted.bed
taf-bedtools bedtools coverage -a regions.bed -b alignments.bam
taf-bedtools bedtools genomecov -i intervals.bed -g genome.txt
taf-bedtools bedtools getfasta -fi reference.fa -bed intervals.bed
```

Because this is a command-mode TAFFISH tool, the first non-option argument is
treated as a command available inside the container image. The recommended
modern interface is:

```sh
taf-bedtools bedtools <SUBCOMMAND> [OPTIONS...]
```

BEDTools also installs historical command aliases such as `intersectBed`,
`sortBed`, and `genomeCoverageBed`. These are available directly:

```sh
taf-bedtools intersectBed -a query.bed -b targets.bed
taf-bedtools sortBed -i intervals.bed
taf-bedtools genomeCoverageBed -i intervals.bed -g genome.txt
```

Do not use:

```sh
taf-bedtools intersect -a query.bed -b targets.bed
```

`intersect` is a BEDTools subcommand, not a standalone executable in the image.
Use `taf-bedtools bedtools intersect ...` or the legacy alias
`taf-bedtools intersectBed ...`.

When passing an upstream option through the default wrapper, use `--` so the
option is handled by BEDTools instead of the TAFFISH wrapper:

```sh
taf-bedtools -- --help
taf-bedtools -- --version
```

For most day-to-day use, explicit command mode is clearer:

```sh
taf-bedtools bedtools --help
taf-bedtools bedtools --version
```

## Package

```text
name: bedtools
command: taf-bedtools
version: 2.31.1-r1
kind: tool
image: ghcr.io/taffish/bedtools:2.31.1-r1
```

## Container

The container image is built from `docker/Dockerfile`. It compiles BEDTools
2.31.1 from the upstream GitHub release tarball in a builder stage, then copies
the installed runtime into a slim runtime image.

The image includes:

```text
bedtools
intersectBed
sortBed
genomeCoverageBed
coverageBed
mergeBed
closestBed
subtractBed
windowBed
fastaFromBed
```

The actual image contains the full set of BEDTools executables installed by the
upstream build, including the modern `bedtools` driver and the historical
`*Bed` command aliases.

The runtime includes the libraries needed by the compiled BEDTools binary:
`zlib`, `bzip2`, `xz/lzma`, and the standard C/C++ runtime. Python is used only
while building BEDTools historical command aliases and is not included in the
runtime image.

The TAFFISH metadata declares a Docker smoke check:

```text
exist: bedtools, intersectBed, sortBed, genomeCoverageBed
test:  bedtools --version | grep -F 'bedtools v2.31.1' >/dev/null
test:  bedtools --help >/dev/null
test:  bedtools intersect -h 2>&1 | grep -F 'Tool:    bedtools intersect' >/dev/null
test:  bedtools sort -h 2>&1 | grep -F 'Tool:    bedtools sort' >/dev/null
test:  bedtools merge -h 2>&1 | grep -F 'Tool:    bedtools merge' >/dev/null
test:  bedtools genomecov -h 2>&1 | grep -F 'Tool:    bedtools genomecov' >/dev/null
test:  intersectBed -h 2>&1 | grep -F 'Tool:    bedtools intersect' >/dev/null
test:  minimal BED intersect functional check
```

During TAFFISH Hub indexing, this smoke metadata verifies that the published
image exposes the modern command, representative legacy aliases, core help
paths, the expected upstream version, and a minimal interval-overlap workflow.

## Upstream

- Project: BEDTools
- Documentation: <https://bedtools.readthedocs.io/>
- Source: <https://github.com/arq5x/bedtools2>
- Release tags: <https://github.com/arq5x/bedtools2/releases>
- Upstream license: MIT

## Maintainer Notes

Useful checks before publishing:

```sh
taf check
taf compile -- bedtools --version
taf compile -- bedtools intersect -h
taf publish --release --dry-run
docker build --check -f docker/Dockerfile .
docker build -t ghcr.io/taffish/bedtools:2.31.1-r1 -f docker/Dockerfile .
docker run --rm ghcr.io/taffish/bedtools:2.31.1-r1 bedtools --version
docker run --rm ghcr.io/taffish/bedtools:2.31.1-r1 intersectBed -h
```

The repository wrapper files are licensed under Apache-2.0. Upstream BEDTools
is distributed under the MIT license, and third-party runtime components are
distributed under their own upstream licenses.
