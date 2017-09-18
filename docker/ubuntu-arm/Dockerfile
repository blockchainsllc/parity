FROM debian:stretch as builder
WORKDIR /build/parity

ENV PATH /root/.cargo/bin:$PATH
ENV RUST_BACKTRACE 1
ENV RUST_TARGETS "arm-unknown-linux-gnueabihf"
ENV LD_LIBRARY_PATH /usr/lib/arm-linux-gnueabihf/

# build dependencies from debian archive
RUN dpkg --add-architecture armhf \
        && apt-get -y update \
        && apt-get install -y --force-yes --no-install-recommends \
               curl \
               git \
               make \
               g++ \
               gcc-arm-linux-gnueabihf \
               g++-arm-linux-gnueabihf \
               libc6-dev-armhf-cross \
               file \
               ca-certificates \
               binutils-arm-linux-gnueabihf \
               libssl-dev:armhf \
               libudev-dev:armhf \
               libstdc++6:armhf \
               libc6:armhf \
        && apt-get clean

# rust and cargo as dependency
RUN  curl https://sh.rustup.rs -sSf | sh -s -- -y \
	&& rustc -vV \
        && cargo -V \
        && rustup target add armv7-unknown-linux-gnueabihf

# pull dependencies and setup target
ADD . /build/parity/
RUN mkdir -p .cargo \
        && echo '[target.armv7-unknown-linux-gnueabihf]\n\
           linker = "arm-linux-gnueabihf-gcc"\n'\
        >>.cargo/config \
        && cat .cargo/config

RUN OPENSSL_DIR=/usr OPENSSL_LIB_DIR=/usr/lib/arm-linux-gnueabihf/ \
           cargo update

# build parity
RUN OPENSSL_DIR=/usr OPENSSL_LIB_DIR=/usr/lib/arm-linux-gnueabihf/ \
           cargo build --target armv7-unknown-linux-gnueabihf --release --verbose \
        && ls /build/parity/target/armv7-unknown-linux-gnueabihf/release/parity

ADD . /build/parity
# strip parity
RUN /usr/bin/arm-linux-gnueabihf-strip /build/parity/target/armv7-unknown-linux-gnueabihf/release/parity \
        && cp -a /build/parity/target/armv7-unknown-linux-gnueabihf/release/parity /parity \
        && file /parity
               
FROM arm32v7/debian:stretch

COPY --from=builder /parity /parity

EXPOSE 8080 8545 8180
ENTRYPOINT ["/parity"]


