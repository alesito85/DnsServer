FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build

ENV DOTNET_CLI_TELEMETRY_OPTOUT=1 \
    DOTNET_EnableDiagnostics=0

WORKDIR /opt/build

RUN apt-get update \
    && apt-get install -y --no-install-recommends git ca-certificates \
    && rm -rf /var/lib/apt/lists/*

RUN git clone --depth=1 https://github.com/TechnitiumSoftware/TechnitiumLibrary.git TechnitiumLibrary \
    && dotnet build TechnitiumLibrary/TechnitiumLibrary.ByteTree/TechnitiumLibrary.ByteTree.csproj -c Release \
    && dotnet build TechnitiumLibrary/TechnitiumLibrary.Net/TechnitiumLibrary.Net.csproj -c Release \
    && dotnet build TechnitiumLibrary/TechnitiumLibrary.Security.OTP/TechnitiumLibrary.Security.OTP.csproj -c Release

RUN git clone --depth=1 https://github.com/alesito85/DnsServer.git DnsServer \
    && dotnet publish DnsServer/DnsServerApp/DnsServerApp.csproj -c Release -o /opt/dnsserver/publish

FROM mcr.microsoft.com/dotnet/aspnet:9.0

ENV DOTNET_CLI_TELEMETRY_OPTOUT=1 \
    DOTNET_EnableDiagnostics=0

WORKDIR /opt/dnsserver/publish

RUN mkdir -p /etc/dns

COPY --from=build /opt/dnsserver/publish .

VOLUME ["/etc/dns"]

EXPOSE \
  53/udp 53/tcp \
  853/udp 853/tcp \
  8443/udp 8443/tcp \
  880/tcp 8053/tcp \
  5380/tcp 53443/tcp

ENTRYPOINT ["dotnet", "DnsServerApp.dll"]
CMD ["/etc/dns"]
