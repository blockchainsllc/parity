FROM debian:stretch
WORKDIR /build

ENV PATH /root/.cargo/bin:$PATH
ENV RUST_BACKTRACE 1
ENV RUST_TARGETS "arm-unknown-linux-gnueabihf"
ENV LD_LIBRARY_PATH /usr/lib/arm-linux-gnueabihf/

# build parity
ADD . /build/parity

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
        && apt-get clean \
	&& curl https://sh.rustup.rs -sSf | sh -s -- -y \
	&& rustc -vV \
        && cargo -V \
        && rustup target add armv7-unknown-linux-gnueabihf \
        && cd parity \
        && mkdir -p .cargo \
        && echo '[target.armv7-unknown-linux-gnueabihf]\n\
           linker = "arm-linux-gnueabihf-gcc"\n'\
        >>.cargo/config \
        && cat .cargo/config \
        && OPENSSL_DIR=/usr \
           OPENSSL_LIB_DIR=/usr/lib/arm-linux-gnueabihf/ \
           cargo build --target armv7-unknown-linux-gnueabihf --release --verbose \
        && ls /build/parity/target/armv7-unknown-linux-gnueabihf/release/parity \
        && /usr/bin/arm-linux-gnueabihf-strip /build/parity/target/armv7-unknown-linux-gnueabihf/release/parity \
        && cp -a /build/parity/target/armv7-unknown-linux-gnueabihf/release/parity /parity \
        && cargo clean \
        && apt-get purge -y \
               gcc-arm-linux-gnueabihf \
               g++-arm-linux-gnueabihf \
               libc6-dev-armhf-cross \
               git \
               make \
               binutils-arm-linux-gnueabihf \
               g++ \
               wget \
        && apt-get autoremove --purge -y \
        && rm -f /etc/ld.so.cache \
        && file /parity
               

EXPOSE 8080 8545 8180
ENTRYPOINT ["/parity"]

