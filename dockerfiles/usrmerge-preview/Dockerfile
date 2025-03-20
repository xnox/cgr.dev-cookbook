# SPDX-FileCopyrightText: 2025 Chainguard, Inc.
# SPDX-License-Identifier: Apache-2.0

# An approximation of what usrmerged containers will look like
# This may only work on a limited amount of images

# Usage example:
#
# docker build --no-cache --build-arg FROM=cgr.dev/chainguard/jdk:latest-dev --tag jdk-usrmerged  .

ARG FROM=cgr.dev/chainguard/jdk:latest-dev
FROM ${FROM}

USER root
RUN set -e; \
    mv /usr/sbin/* /usr/bin/; \
    rmdir /usr/sbin; \
    ln -sf bin /usr/sbin; \
    cp /lib/ld-* /usr/lib/; \
    /usr/lib/ld-* --inhibit-cache /usr/bin/busybox mv /lib/* /usr/lib/; \
    /usr/lib/ld-* --inhibit-cache /usr/bin/busybox rmdir /lib; \
    /usr/lib/ld-* --inhibit-cache /usr/bin/busybox ln -s usr/lib /lib
