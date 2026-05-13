ARG BEDTOOLS_VERSION=2.31.1

FROM debian:12-slim AS builder

ENV DEBIAN_FRONTEND=noninteractive
ARG BEDTOOLS_VERSION

RUN set -eux; \
    for attempt in 1 2 3; do \
      if apt-get -o Acquire::Retries=3 update \
       && apt-get -o Acquire::Retries=3 install -y --no-install-recommends \
            build-essential \
            ca-certificates \
            curl \
            libbz2-dev \
            liblzma-dev \
            python3 \
            zlib1g-dev; then \
        break; \
      fi; \
      if [ "$attempt" = "3" ]; then exit 1; fi; \
      apt-get clean; \
      rm -rf /var/lib/apt/lists/*; \
      sleep 5; \
    done; \
    rm -rf /var/lib/apt/lists/*

WORKDIR /tmp/build/bedtools

RUN curl -fsSL "https://github.com/arq5x/bedtools2/releases/download/v${BEDTOOLS_VERSION}/bedtools-${BEDTOOLS_VERSION}.tar.gz" \
      -o bedtools.tar.gz \
 && tar -xzf bedtools.tar.gz --strip-components=1 \
 && make -j"$(nproc)" \
 && mkdir -p /opt/bedtools/bin \
 && cp -a bin/. /opt/bedtools/bin/ \
 && /opt/bedtools/bin/bedtools --version | grep -F "bedtools v${BEDTOOLS_VERSION}" >/dev/null \
 && /opt/bedtools/bin/bedtools --help >/dev/null \
 && /opt/bedtools/bin/bedtools intersect -h 2>&1 | grep -F "Tool:    bedtools intersect" >/dev/null \
 && /opt/bedtools/bin/bedtools sort -h 2>&1 | grep -F "Tool:    bedtools sort" >/dev/null \
 && /opt/bedtools/bin/bedtools genomecov -h 2>&1 | grep -F "Tool:    bedtools genomecov" >/dev/null

FROM debian:12-slim

ENV DEBIAN_FRONTEND=noninteractive
ARG BEDTOOLS_VERSION

RUN set -eux; \
    for attempt in 1 2 3; do \
      if apt-get -o Acquire::Retries=3 update \
       && apt-get -o Acquire::Retries=3 install -y --no-install-recommends \
            libbz2-1.0 \
            liblzma5 \
            zlib1g; then \
        break; \
      fi; \
      if [ "$attempt" = "3" ]; then exit 1; fi; \
      apt-get clean; \
      rm -rf /var/lib/apt/lists/*; \
      sleep 5; \
    done; \
    rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/bedtools /opt/bedtools

ENV PATH=/opt/bedtools/bin:${PATH}

RUN bedtools --version | grep -F "bedtools v${BEDTOOLS_VERSION}" >/dev/null \
 && bedtools --help >/dev/null \
 && bedtools intersect -h 2>&1 | grep -F "Tool:    bedtools intersect" >/dev/null \
 && bedtools sort -h 2>&1 | grep -F "Tool:    bedtools sort" >/dev/null \
 && bedtools genomecov -h 2>&1 | grep -F "Tool:    bedtools genomecov" >/dev/null

RUN set -eux; \
    mkdir -p /tmp/bedtools-test; \
    cd /tmp/bedtools-test; \
    printf "chr1\t0\t10\nchr1\t20\t30\n" > a.bed; \
    printf "chr1\t5\t12\n" > b.bed; \
    printf "chr1\t100\n" > genome.txt; \
    bedtools intersect -a a.bed -b b.bed | grep -F "chr1	5	10" >/dev/null; \
    intersectBed -a a.bed -b b.bed | grep -F "chr1	5	10" >/dev/null; \
    bedtools sort -i a.bed >/dev/null; \
    bedtools genomecov -i a.bed -g genome.txt >/dev/null; \
    cd /; \
    rm -rf /tmp/bedtools-test

WORKDIR /root

ENV TAFFISH_ENV=TAFFISH
ENV TAFFISH_NAME=bedtools
