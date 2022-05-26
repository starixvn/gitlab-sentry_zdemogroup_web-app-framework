# escape=`

FROM registry.nexdev.net:5050/nexcel/nexops/rocketor/buildservice:base AS builder

SHELL ["cmd", "/S", "/C"]

WORKDIR /src


FROM registry.nexdev.net:5050/docker/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019 AS base

WORKDIR /app

COPY src/ ./

ENTRYPOINT ["C:\\LogMonitor\\LogMonitor.exe","Fanex.WebDeployment.BuildService.exe"]