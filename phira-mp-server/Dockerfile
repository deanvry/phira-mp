# app builder

FROM ubuntu:jammy AS builder

RUN sed -i 's/archive.ubuntu.com/sg.archive.ubuntu.com/g' /etc/apt/sources.list
RUN apt-get update
RUN apt-get install -y \
    curl \
    git \
    build-essential \
    pkg-config \
    libssl-dev

WORKDIR /root

RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
ENV PATH="/root/.cargo/bin:${PATH}"

RUN git clone https://github.com/TeamFlos/phira-mp
RUN cd phira-mp && \
    cargo build --release -p phira-mp-server


# app server

FROM ubuntu:jammy

# ca-certificates is required to connect securely to https://api.phira.cn
RUN sed -i 's/archive.ubuntu.com/sg.archive.ubuntu.com/g' /etc/apt/sources.list
RUN apt-get update
RUN apt-get install -y ca-certificates

RUN mkdir /app
COPY --from=builder /root/phira-mp/target/release/phira-mp-server /app/phira-mp-server
RUN chmod +x /app/phira-mp-server

WORKDIR /app
ENV RUST_LOG=info
EXPOSE 5000
CMD ["/app/phira-mp-server", "--port", "5000"]
