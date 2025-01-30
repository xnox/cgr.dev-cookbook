# SPDX-FileCopyrightText: 2025 Chainguard, Inc.
# SPDX-License-Identifier: Apache-2.0

# Pull unmodified base image
FROM cgr.dev/chainguard/glibc-dynamic:latest AS base

# Pull build container with toolchains and apk
FROM cgr.dev/chainguard/gcc-glibc:latest-dev AS build

# Get workload source code
COPY <<EOF ./test.c
#include <stdio.h>
#include <curl/curl.h>
void main(){
    printf("%s\\n", curl_version());
}
EOF

# Install build-time dependency
RUN apk add pc:libcurl

# Compile workload code
RUN gcc test.c `pkg-config --cflags --libs libcurl` -o dynamic-binary

# Copy base image contents to a subfolder
COPY --from=base / /base-chroot

# Customize base image chroot
RUN apk add --root /base-chroot so:libcurl.so.4

# Create customized production image from scratch
FROM scratch AS custom-production-image
# Copy customized base image
COPY --from=build /base-chroot /
# Copy workload binary
COPY --from=build /work/dynamic-binary /usr/bin/dynamic-binary

ENTRYPOINT ["/usr/bin/dynamic-binary"]
