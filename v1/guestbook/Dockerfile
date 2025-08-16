# ---------- Stage 1: Build ----------
    FROM golang:1.20 AS builder
    WORKDIR /app

    # Copy go.mod and go.sum first (better caching)
    COPY go.mod go.sum ./
    RUN go mod download

    # copy source and build static binary
    COPY . .
    RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o main .

    # ---------- Stage 2: Runtime ----------
    FROM ubuntu:18.04
    WORKDIR /app

    # minimal runtime deps (SSL certs)
    RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*

    # copy built app + static file
    COPY --from=builder /app/main /app/guestbook
    COPY public/index.html /app/public/index.html

    # app listens on 3000 (matches your deployment.yml)
    EXPOSE 3000

    # start the app
    CMD ["/app/guestbook"]
