ARG PG_MAJOR=18
ARG EXT_VERSION=2.31.0
ARG DISTRO=trixie
ARG BASE=ghcr.io/cloudnative-pg/postgresql:${PG_MAJOR}-minimal-${DISTRO}

FROM ${BASE} AS builder

ARG PG_MAJOR
ARG EXT_VERSION

USER 0

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl gpg lsb-release ca-certificates && \
    curl -fsSL https://packagecloud.io/timescale/timescaledb/gpgkey | gpg --dearmor -o /etc/apt/trusted.gpg.d/timescaledb.gpg && \
    echo "deb [signed-by=/etc/apt/trusted.gpg.d/timescaledb.gpg] https://packagecloud.io/timescale/timescaledb/debian/ $(lsb_release -c -s) main" | tee /etc/apt/sources.list.d/timescaledb.list && \
    apt-get update && apt-get install -y --no-install-recommends "timescaledb-2-${EXT_VERSION}-postgresql-${PG_MAJOR}"


FROM scratch
ARG PG_MAJOR
ARG EXT_VERSION

# Licenses
COPY --from=builder /usr/share/doc/timescaledb-2-${EXT_VERSION}-postgresql-${PG_MAJOR}/copyright /licenses/postgresql-${PG_MAJOR}-timescaledb/
COPY --from=builder /usr/share/doc/timescaledb-2-loader-postgresql-${PG_MAJOR}/copyright /licenses/timescaledb-loader/copyright

# Libraries
COPY --from=builder /usr/lib/postgresql/${PG_MAJOR}/lib/timescaledb* /lib/

# Share
COPY --from=builder /usr/share/postgresql/${PG_MAJOR}/extension/timescaledb* /share/extension/

USER 65532:65532
