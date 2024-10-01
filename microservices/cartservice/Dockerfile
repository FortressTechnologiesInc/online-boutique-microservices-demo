# Use the official .NET SDK image from Microsoft
FROM mcr.microsoft.com/dotnet/sdk:6.0-alpine as builder
WORKDIR /app
COPY . .

# Restore and build the project
RUN dotnet restore && \
    dotnet build && \
    dotnet publish -c Release -r linux-musl-x64 -o /cartservice

# Runtime image
FROM alpine:3.17

# Install gRPC health probe
RUN GRPC_HEALTH_PROBE_VERSION=v0.2.0 && \
    wget -qO /bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe

# Dependencies for runtime
RUN apk add --no-cache \
    busybox-extras \
    libc6-compat \
    libunwind \
    libuuid \
    libgcc \
    libstdc++ \
    libintl \
    icu

# Set working directory and copy built app
WORKDIR /app
COPY --from=builder /cartservice .

# Command to run the application
ENTRYPOINT ["./cartservice", "start"]
