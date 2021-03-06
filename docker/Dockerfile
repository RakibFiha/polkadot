FROM rust:latest as builder
LABEL description="This is the build stage for Polkadot. Here we create the binary."
ARG PROFILE=release
WORKDIR /polkadot
COPY . /polkadot
ENV DEBIAN_FRONTEND noninteractive
ENV DEBCONF_NOWARNINGS yes
#RUN rustup toolchain install nightly wasm
RUN apt-get update && apt-get install -y \
    libssl-dev \
    clang \
    llvm
RUN rustup toolchain install nightly-2020-08-23
RUN rustup target add wasm32-unknown-unknown --toolchain nightly-2020-08-23
RUN rustup override set nightly-2020-08-23
RUN cargo build --$PROFILE
# ===== SECOND STAGE ======
FROM debian:buster-slim
LABEL description="This is the 2nd stage: a very small image where we copy the Polkadot binary."
ARG PROFILE=release
COPY --from=builder /polkadot/target/$PROFILE/polkadot /usr/local/bin
RUN useradd -m -u 1000 -U -s /bin/sh -d /polkadot polkadot && \
        mkdir -p /polkadot/.local/share/polkadot && \
        chown -R polkadot:polkadot /polkadot/.local && \
        ln -s /polkadot/.local/share/polkadot /data && \
        rm -rf /usr/bin /usr/sbin
USER polkadot
EXPOSE 30333 9933 9944 9945 30945
VOLUME ["/data"]
CMD ["/usr/local/bin/polkadot"]
