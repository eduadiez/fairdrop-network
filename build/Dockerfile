FROM alpine:3.9 as builder

ENV COMMIT v1.8.27

RUN apk add --update git go make gcc musl-dev linux-headers bash pwgen
RUN git clone -b ${COMMIT} https://github.com/ethereum/go-ethereum.git

RUN \
  (cd go-ethereum && make geth )                  && \
  cp go-ethereum/build/bin/geth /usr/local/bin/   && \
  apk del git go make gcc musl-dev linux-headers  && \
  rm -rf /go-ethereum && rm -rf /var/cache/apk/*

# Pull Geth into a second stage deploy alpine container
FROM alpine:latest

RUN apk add --no-cache ca-certificates
COPY --from=builder /usr/local/bin/geth /usr/local/bin/geth

COPY noordung.json /root/.ethereum/
COPY config.toml /root/.ethereum/ 

RUN geth init --datadir=/root/.ethereum/ /root/.ethereum/noordung.json 

ENTRYPOINT ["geth","--config","/root/.ethereum/config.toml"]