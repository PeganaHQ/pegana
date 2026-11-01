FROM rust:1.99-slim@sha256:b8e062b1c3a80183df83d9ba187cadd2b5ab84bc78d453dbfeb2fccd5f982247 AS builder
WORKDIR /build
RUN apt-get update && apt-get install -y --no-install-recommends pkg-config libssl-dev ca-certificates && rm -rf /var/lib/apt/lists/*
COPY Cargo.toml Cargo.lock ./
COPY crates ./crates
# oracle_degraded.rs embeds the [pyth_staleness.overrides] table at compile time.
COPY assets.toml ./
RUN cargo build --release -p api-rs --locked

FROM debian:bookworm-slim@sha256:99c0b25c46d0214d5a2091092e7908907dcaecd6a962cde0ac700acee222b6f3
RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates libssl3 curl && rm -rf /var/lib/apt/lists/*
RUN useradd -r -u 10001 pegana
WORKDIR /app
COPY --from=builder /build/target/release/api-rs /usr/local/bin/api-rs
USER pegana
EXPOSE 8080
ENTRYPOINT ["api-rs"]
