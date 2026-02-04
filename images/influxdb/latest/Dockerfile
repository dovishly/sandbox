ARG INFLUXDB_TAG
FROM influxdb:$INFLUXDB_TAG


# https://github.com/influxdata/inch
USER root
RUN apt-get update && apt-get install -y golang-go && rm -rf /var/lib/apt/lists/*
RUN go install github.com/influxdata/inch/cmd/inch@latest
ENV PATH="$PATH:root/go/bin/"
