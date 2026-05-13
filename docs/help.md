taf-bedtools 2.31.1-r1

TAFFISH wrapper for BEDTools, a command-line toolkit for genome arithmetic on
BED, GFF/GTF, VCF, and BAM files.

Usage:
  taf-bedtools [TAF-APP-OPTION]
  taf-bedtools bedtools [BEDTOOLS-SUBCOMMAND] [COMMAND-ARGS...]
  taf-bedtools intersectBed [INTERSECT-ARGS...]
  taf-bedtools sortBed [SORT-ARGS...]
  taf-bedtools genomeCoverageBed [GENOMECOV-ARGS...]
  taf-bedtools -- [BEDTOOLS-OPTION...]
  taf-bedtools <COMMAND> [COMMAND-ARGS...]

TAF app options:
  -h, --help       Show this help text
  -v, --version    Show package and command version
  --compile        Print generated shell code instead of running it
  --               Stop parsing TAFFISH wrapper options

Upstream help:
  taf-bedtools bedtools --help
  taf-bedtools bedtools --version
  taf-bedtools bedtools intersect -h
  taf-bedtools bedtools sort -h
  taf-bedtools bedtools genomecov -h

Recommended BEDTools examples:
  taf-bedtools bedtools intersect -a query.bed -b targets.bed
  taf-bedtools bedtools sort -i intervals.bed > intervals.sorted.bed
  taf-bedtools bedtools merge -i intervals.sorted.bed
  taf-bedtools bedtools coverage -a regions.bed -b alignments.bam
  taf-bedtools bedtools genomecov -i intervals.bed -g genome.txt
  taf-bedtools bedtools getfasta -fi reference.fa -bed intervals.bed
  taf-bedtools bedtools closest -a query.bed -b annotations.bed
  taf-bedtools bedtools subtract -a regions.bed -b blacklist.bed

Legacy alias examples:
  taf-bedtools intersectBed -a query.bed -b targets.bed
  taf-bedtools sortBed -i intervals.bed
  taf-bedtools genomeCoverageBed -i intervals.bed -g genome.txt
  taf-bedtools mergeBed -i intervals.sorted.bed

Common BEDTools subcommands:
  intersect
  window
  closest
  coverage
  map
  genomecov
  merge
  cluster
  complement
  subtract
  slop
  flank
  sort
  getfasta
  maskfasta
  nuc
  bamtobed
  bedtobam
  bamtofastq
  multicov
  jaccard
  reldist
  fisher
  makewindows
  groupby
  summary

Notes:
  - This command runs BEDTools inside the TAFFISH container image.
  - Use "taf-bedtools bedtools <SUBCOMMAND> ..." for ordinary modern BEDTools
    workflows.
  - BEDTools also installs historical aliases such as "intersectBed",
    "sortBed", "genomeCoverageBed", "mergeBed", and "coverageBed"; these can
    be called directly with "taf-bedtools <ALIAS> ...".
  - BEDTools subcommands such as "intersect", "sort", "merge", and
    "genomecov" are not standalone executables in the container image. Do not
    use "taf-bedtools intersect ..."; use "taf-bedtools bedtools intersect ..."
    or "taf-bedtools intersectBed ...".
  - Use "--" before upstream options when an option may be handled by the
    TAFFISH wrapper itself, such as "--help" or "--version".
  - Input and output paths should be accessible from the current working
    directory or from mounted user paths.
  - This image packages upstream BEDTools 2.31.1 in a Debian 12 runtime image.

Container:
  image: ghcr.io/taffish/bedtools:2.31.1-r1
  supported backends: apptainer, podman, docker

Upstream:
  project: BEDTools
  documentation: https://bedtools.readthedocs.io/
  source:        https://github.com/arq5x/bedtools2
