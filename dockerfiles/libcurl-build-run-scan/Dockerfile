# SPDX-FileCopyrightText: 2025 Chainguard, Inc.
# SPDX-License-Identifier: Apache-2.0

# Pull build container with apk, and optionally toolchains
FROM cgr.dev/chainguard/gcc-glibc:latest-dev AS build

# Link existing single layer base image to customize
COPY --link --from=cgr.dev/chainguard/glibc-dynamic:latest / /base-chroot

# Customize final image by adding packages to it
RUN apk add --no-cache --root /base-chroot curl so:libcurl.so.4

## Optionally install toolchain dependencies & compile custom workload
# Install build-time dependency
RUN apk add pc:libcurl
# Get workload source code
COPY <<EOF ./test.c
#include <stdio.h>
#include <curl/curl.h>
void main(){
    printf("%s\\n", curl_version());
}
EOF
# Compile workload code
RUN gcc test.c `pkg-config --cflags --libs libcurl` -o /base-chroot/usr/bin/dynamic-binary

# Create customized production image, use the same from as base image
# to preserve all the image settings.  If original base settings are
# not important, but smallest final image size is, use FROM scratch
# here.
FROM cgr.dev/chainguard/glibc-dynamic:latest AS final
COPY --link --from=build /base-chroot /

ENTRYPOINT ["/usr/bin/dynamic-binary"]
