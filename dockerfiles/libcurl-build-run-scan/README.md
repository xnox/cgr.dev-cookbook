<!--
SPDX-FileCopyrightText: 2025 Chainguard, Inc.
SPDX-License-Identifier: Apache-2.0
-->

## Multi-stage distroless build

Use chainguard reference runtime & build containers to create
customized distroless runtime container with target workload.


This is achieved by:
 * pulling reference base & development images.
 * Adding appropriate packages to custom runtime image
 * Adding appropriate packages to custom buildtime image
 * Compiling target workload

At the end final production image is assmebled from:
 * Scratch
 * Customized runtime
 * Target workload

This example below uses:
 * gcc-glibc toolchain image dev tag as build
 * glibc-dynamic as the reference runtime image as base
 * adds libcurl development library to build (95 packages total)
 * adds libcurl runtime libraries to base (33 packages total)
 * compiles tiny C binary that requires libcurl

The resulting image is minimal, distroless, and compatible with popular security scanners.

> [!NOTE]
> Always build with `--no-cache` to ensure latest remediated binaries are used.

> [!WARNING]
> This image has to be rebuilt periodically, even when reference base
> & development images did not change to get updates of custom
> packages


## Demo

### Build

```
$ docker build --progress plain --no-cache . --tag dynamic-binary

```

<details><summary>Build output</summary>

```
$ docker build --progress plain --no-cache . --tag dynamic-binary
#0 building with "default" instance using docker driver

#1 [internal] load build definition from Dockerfile
#1 transferring dockerfile: 1.04kB done
#1 DONE 0.0s

#2 [internal] load metadata for cgr.dev/chainguard/gcc-glibc:latest-dev
#2 DONE 0.0s

#3 [internal] load metadata for cgr.dev/chainguard/glibc-dynamic:latest
#3 ...

#4 [auth] chainguard/glibc-dynamic:pull token for cgr.dev
#4 DONE 0.0s

#3 [internal] load metadata for cgr.dev/chainguard/glibc-dynamic:latest
#3 DONE 1.5s

#5 [internal] load .dockerignore
#5 transferring context: 2B done
#5 DONE 0.0s

#6 [internal] preparing inline document
#6 CACHED

#7 [build 1/6] FROM cgr.dev/chainguard/gcc-glibc:latest-dev
#7 CACHED

#8 [build 2/6] COPY <<EOF ./test.c
#8 DONE 0.0s

#9 [base 1/1] FROM cgr.dev/chainguard/glibc-dynamic:latest@sha256:c907cf5576de12bb54ac2580a91d0287de55f56bce4ddd66a0edf5ebaba9feed
#9 resolve cgr.dev/chainguard/glibc-dynamic:latest@sha256:c907cf5576de12bb54ac2580a91d0287de55f56bce4ddd66a0edf5ebaba9feed 0.0s done
#9 sha256:c907cf5576de12bb54ac2580a91d0287de55f56bce4ddd66a0edf5ebaba9feed 939B / 939B done
#9 sha256:e1de8e6414c8e896a967ffc998e8ca3804c3a0708293e9ff6db3f3e89db85c53 853B / 853B done
#9 sha256:ff6d6a299efd8350bacd0fe01b4276ac546aef689bb97cdbfc9230105f5e7709 952B / 952B done
#9 sha256:74162ef4f0a60dfc9eb1e6fa68c064a5c4962414fae6e7c6597ee48c485c7125 0B / 4.35MB 0.1s
#9 sha256:74162ef4f0a60dfc9eb1e6fa68c064a5c4962414fae6e7c6597ee48c485c7125 2.10MB / 4.35MB 0.4s
#9 sha256:74162ef4f0a60dfc9eb1e6fa68c064a5c4962414fae6e7c6597ee48c485c7125 4.35MB / 4.35MB 0.4s done
#9 extracting sha256:74162ef4f0a60dfc9eb1e6fa68c064a5c4962414fae6e7c6597ee48c485c7125 0.0s done
#9 DONE 0.5s

#10 [build 3/6] RUN apk add pc:libcurl
#10 0.127 fetch https://packages.wolfi.dev/os/x86_64/APKINDEX.tar.gz
#10 1.992 (1/33) Installing libbrotlienc1 (1.1.0-r4)
#10 2.089 (2/33) Installing brotli (1.1.0-r4)
#10 2.159 (3/33) Installing brotli-dev (1.1.0-r4)
#10 2.675 (4/33) Installing libpsl-dev (0.21.5-r4)
#10 2.753 (5/33) Installing c-ares (1.34.4-r0)
#10 2.945 (6/33) Installing libev (4.33-r6)
#10 3.013 (7/33) Installing nghttp2 (1.64.0-r1)
#10 3.175 (8/33) Installing nghttp2-dev (1.64.0-r1)
#10 3.358 (9/33) Installing jitterentropy-library (3.6.1-r0)
#10 3.560 (10/33) Installing jitterentropy-library-dev (3.6.1-r0)
#10 3.626 (11/33) Installing openssl-dev (3.4.0-r6)
#10 4.138 (12/33) Installing e2fsprogs-libs (1.47.2-r0)
#10 4.345 (13/33) Installing gawk (5.3.1-r1)
#10 4.665 (14/33) Installing sqlite (3.48.0-r0)
#10 4.777 (15/33) Installing sqlite-dev (3.48.0-r0)
#10 5.039 (16/33) Installing libblkid (2.40.4-r0)
#10 5.134 (17/33) Installing libuuid (2.40.4-r0)
#10 5.197 (18/33) Installing libfdisk (2.40.4-r0)
#10 5.292 (19/33) Installing util-linux (2.40.4-r0)
#10 5.363 (20/33) Installing libmount (2.40.4-r0)
#10 5.446 (21/33) Installing libsmartcols (2.40.4-r0)
#10 5.521 (22/33) Installing util-linux-dev (2.40.4-r0)
#10 5.748 (23/33) Installing e2fsprogs-dev (1.47.2-r0)
#10 5.802 (24/33) Installing libffi (3.4.6-r5)
#10 5.853 (25/33) Installing glib (2.83.3-r0)
#10 6.034 (26/33) Installing libverto-glib (0.3.2-r4)
#10 6.085 (27/33) Installing libverto-libev (0.3.2-r4)
#10 6.131 (28/33) Installing libverto-libevent (0.3.2-r4)
#10 6.173 (29/33) Installing libverto-dev (0.3.2-r4)
#10 6.227 (30/33) Installing krb5-server-ldap (1.21.3-r2)
#10 6.282 (31/33) Installing krb5-dev (1.21.3-r2)
#10 6.370 (32/33) Installing zlib-dev (1.3.1-r5)
#10 6.418 (33/33) Installing curl-dev (8.11.1-r0)
#10 6.516 Executing glibc-2.40-r8.trigger
#10 6.532 Executing busybox-1.37.0-r0.trigger
#10 6.542 OK: 622 MiB in 95 packages
#10 DONE 6.6s

#11 [build 4/6] RUN gcc test.c `pkg-config --cflags --libs libcurl` -o dynamic-binary
#11 DONE 0.3s

#12 [build 5/6] COPY --from=base / /base-chroot
#12 DONE 0.0s

#13 [build 6/6] RUN apk add --root /base-chroot so:libcurl.so.4
#13 0.218 fetch https://packages.wolfi.dev/os/x86_64/APKINDEX.tar.gz
#13 1.184 (1/26) Installing libbrotlicommon1 (1.1.0-r4)
#13 1.257 (2/26) Installing libbrotlidec1 (1.1.0-r4)
#13 1.536 (3/26) Installing libcrypto3 (3.4.0-r6)
#13 1.693 (4/26) Installing krb5-conf (1.0-r3)
#13 1.759 (5/26) Installing libcom_err (1.47.2-r0)
#13 1.804 (6/26) Installing keyutils-libs (1.6.3-r5)
#13 1.854 (7/26) Installing libssl3 (3.4.0-r6)
#13 1.929 (8/26) Installing libverto (0.3.2-r4)
#13 1.982 (9/26) Installing krb5-libs (1.21.3-r2)
#13 2.384 (10/26) Installing libevent (2.1.12-r6)
#13 2.550 (11/26) Installing libxcrypt (4.4.38-r0)
#13 2.610 (12/26) Installing libcrypt1 (2.40-r8)
#13 2.668 (13/26) Installing ncurses-terminfo-base (6.5_p20241228-r0)
#13 2.740 (14/26) Installing ncurses (6.5_p20241228-r0)
#13 2.824 (15/26) Installing readline (8.2.13-r1)
#13 2.893 (16/26) Installing sqlite-libs (3.48.0-r0)
#13 2.986 (17/26) Installing heimdal (7.8.0-r6)
#13 3.097 (18/26) Installing gdbm (1.24-r2)
#13 3.159 (19/26) Installing cyrus-sasl (2.1.28-r5)
#13 3.229 (20/26) Installing libldap (2.6.9-r0)
#13 3.285 (21/26) Installing libnghttp2-14 (1.64.0-r1)
#13 3.335 (22/26) Installing libunistring (1.3-r1)
#13 3.430 (23/26) Installing libidn2 (2.3.7-r3)
#13 3.719 (24/26) Installing libpsl (0.21.5-r4)
#13 3.777 (25/26) Installing zlib (1.3.1-r5)
#13 3.834 (26/26) Installing libcurl-openssl4 (8.11.1-r0)
#13 3.907 Executing glibc-2.40-r8.trigger
#13 3.911 ERROR: glibc-2.40-r8.trigger: script exited with error 127
#13 3.914 OK: 35 MiB in 33 packages
#13 DONE 4.0s

#14 [custom-production-image 1/2] COPY --from=build /base-chroot /
#14 DONE 0.1s

#15 [custom-production-image 2/2] COPY --from=build /work/dynamic-binary /usr/bin/dynamic-binary
#15 DONE 0.0s

#16 exporting to image
#16 exporting layers 0.1s done
#16 writing image sha256:c67e3db0545f7f16150c68da30a543c8a7e1a13fb0a59f0d8ba652e7e3d8db87 done
#16 naming to docker.io/library/dynamic-binary done
#16 DONE 0.1s
```
</details>

Observe that build image contains shell and is able to execute shell script triggers:
```
#10 6.516 Executing glibc-2.40-r8.trigger
#10 6.532 Executing busybox-1.37.0-r0.trigger
#10 6.542 OK: 622 MiB in 95 packages
#10 DONE 6.6s
```

And custom base image cannot, as it has no shell:
```
#13 3.907 Executing glibc-2.40-r8.trigger
#13 3.911 ERROR: glibc-2.40-r8.trigger: script exited with error 127
#13 3.914 OK: 35 MiB in 33 packages
#13 DONE 4.0s

```

### Test

```
$ docker run -ti dynamic-binary
libcurl/8.11.1 OpenSSL/3.4.0 zlib/1.3.1 brotli/1.1.0 libpsl/0.21.5 nghttp2/1.64.0 OpenLDAP/2.6.9
```

### Scan

```
$ syft dynamic-binary
 ✔ Loaded image                                                                                                                      dynamic-binary:latest
 ✔ Parsed image                                                                    sha256:c67e3db0545f7f16150c68da30a543c8a7e1a13fb0a59f0d8ba652e7e3d8db87
 ✔ Cataloged contents                                                                     e0dc266392797dfe58b927cf4dd0c7fc7639139eddff4c1c6370e8b1a6d4c2b8
   ├── ✔ Packages                        [33 packages]  
   ├── ✔ File digests                    [518 files]  
   ├── ✔ File metadata                   [518 locations]  
   └── ✔ Executables                     [144 executables]  
NAME                    VERSION           TYPE   
ca-certificates-bundle  20241121-r1       apk     
cyrus-sasl              2.1.28-r5         apk     
gdbm                    1.24-r2           apk     
glibc                   2.40-r8           apk     
glibc-locale-posix      2.40-r8           apk     
heimdal                 7.8.0-r6          apk     
keyutils-libs           1.6.3-r5          apk     
krb5-conf               1.0-r3            apk     
krb5-libs               1.21.3-r2         apk     
ld-linux                2.40-r8           apk     
libbrotlicommon1        1.1.0-r4          apk     
libbrotlidec1           1.1.0-r4          apk     
libcom_err              1.47.2-r0         apk     
libcrypt1               2.40-r8           apk     
libcrypto3              3.4.0-r6          apk     
libcurl-openssl4        8.11.1-r0         apk     
libevent                2.1.12-r6         apk     
libgcc                  14.2.0-r8         apk     
libidn2                 2.3.7-r3          apk     
libldap                 2.6.9-r0          apk     
libnghttp2-14           1.64.0-r1         apk     
libpsl                  0.21.5-r4         apk     
libssl3                 3.4.0-r6          apk     
libstdc++               14.2.0-r8         apk     
libunistring            1.3-r1            apk     
libverto                0.3.2-r4          apk     
libxcrypt               4.4.38-r0         apk     
ncurses                 6.5_p20241228-r0  apk     
ncurses-terminfo-base   6.5_p20241228-r0  apk     
readline                8.2.13-r1         apk     
sqlite-libs             3.48.0-r0         apk     
wolfi-baselayout        20230201-r16      apk     
zlib                    1.3.1-r5          apk   
```
