# https://mcr.microsoft.com/v2/dotnet/sdk/tags/list
FROM mcr.microsoft.com/dotnet/sdk:5.0.402 AS compile

WORKDIR /app

COPY cartservice.csproj .
RUN dotnet restore cartservice.csproj -r linux-musl-x64
COPY . .
RUN dotnet publish cartservice.csproj -p:PublishSingleFile=true \
    -r linux-musl-x64 \
    --self-contained true \
    -p:PublishTrimmed=True \
    -p:TrimMode=Link -c release -o /cartservice --no-restore

# https://mcr.microsoft.com/v2/dotnet/runtime-deps/tags/list
FROM mcr.microsoft.com/dotnet/runtime-deps:5.0.11-alpine3.14-amd64 AS release

RUN GRPC_HEALTH_PROBE_VERSION=v0.4.6 && \
    wget -qO/bin/grpc_health_probe \
    https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe

WORKDIR /app

COPY --from=compiler /cartservice .

ENV ASPNETCORE_URLS http://*:7070
ENTRYPOINT ["/app/cartservice"]