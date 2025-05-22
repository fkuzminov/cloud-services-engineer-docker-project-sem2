FROM golang:1.17-alpine AS builder

WORKDIR /app
COPY go.mod .
RUN go mod download
COPY . .
ENV CGO_ENABLED=0
RUN go build \
    -trimpath \
    -ldflags="-s -w" \
    -o /out/app ./cmd/api

FROM alpine:3.15
RUN adduser -D -s /sbin/nologin nonroot
COPY --from=builder /out/app /app
RUN chown nonroot:nonroot /app && chmod +x /app
USER nonroot

HEALTHCHECK --interval=20s --timeout=3s --start-period=5s --retries=3 \
    CMD wget -qO- http://127.0.0.1:8081/health || exit 1

EXPOSE 8081
ENTRYPOINT ["/app"]