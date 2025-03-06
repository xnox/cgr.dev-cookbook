# from runtime jdk image, without any tooling, nor possibly even shell
FROM cgr.dev/chainguard-private/jdk:latest
# fetch apk.static from the internet
ADD https://gitlab.alpinelinux.org/api/v4/projects/5/packages/generic/v2.14.6/x86_64/apk.static /opt/apk.static
# use it to add a package
RUN /opt/apk.static --no-cache add curl
# if one has shell, one can RM it, however that will not have any layer disk saving
RUN rm /opt/apk.static
