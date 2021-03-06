FROM alpine:3.12 as builder

RUN mkdir /empty

RUN apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/testing --no-cache \
    curl gcc musl-dev nim tcc

RUN curl -L https://github.com/nim-lang/Nim/archive/v1.4.0.tar.gz | tar xzf - \
 && curl -L https://github.com/nim-lang/csources/archive/v0.20.0.tar.gz | tar xzf -

RUN mv csources-0.20.0 Nim-1.4.0/csources \
 && cd Nim-*/csources                     \
 && ./build.sh                            \
 && cd ..                                 \
 && ./bin/nim compile -d:release koch     \
 && ./koch boot -d:release                \
 && ./koch install /usr/bin               \
 && strip /usr/bin/nim

FROM scratch

COPY --from=0 /bin/sh                  /bin/
COPY          /nim.cfg                 /etc/nim/
COPY --from=0 /lib/ld-musl-x86_64.so.1 /lib/
COPY --from=0 /empty                   /proc
COPY --from=0 /empty                   /tmp
COPY --from=0 /usr/bin/nim             \
              /usr/bin/tcc             /usr/bin/
COPY --from=0 /usr/include             /usr/include/
COPY --from=0 /usr/lib/crt1.o          \
              /usr/lib/crti.o          \
              /usr/lib/crtn.o          \
              /usr/lib/libc.a          \
              /usr/lib/libdl.a         \
              /usr/lib/libm.a          \
              /usr/lib/librt.a         /usr/lib/
COPY --from=0 /usr/lib/nim             /usr/lib/nim/
COPY --from=0 /usr/lib/tcc             /usr/lib/tcc/

ENTRYPOINT ["/usr/bin/nim", "-v"]
