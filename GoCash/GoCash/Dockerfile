# Stage 1: Build stage
FROM golang@sha256:4746d26432a9117a5f58e95cb9f954ddf0de128e9d5816886514199316e4a2fb AS builder

WORKDIR /app

# Copy go.mod and go.sum first for caching dependencies
COPY go.mod go.sum ./
RUN go mod download

# Copy the entire project
COPY . .

# Build the Go binary with static linking
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o gocash ./cmd/cash/main.go

# Stage 2: Minimal runtime image
FROM alpine@sha256:dd60c75fba961ecc5e918961c713f3c42dd5665171c58f9b2ef5aafe081ad5a0

# Add CA certificates and required libraries
RUN apk update && \
    apk add --no-cache ca-certificates libc6-compat

WORKDIR /root/
COPY --from=builder /app/gocash .

# Set the binary as the entrypoint
ENTRYPOINT ["./gocash"]
CMD []
