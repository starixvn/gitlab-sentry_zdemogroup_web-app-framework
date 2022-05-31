# escape=`

FROM registry.nexdev.net:5050/nexcel/nexops/rocketor/buildservice:base AS builder

SHELL ["cmd", "/S", "/C"]

WORKDIR /build

# Copy files *.sln, *.csproj, packages.config to restore nuget packages
COPY ../../*.sln .
COPY *.csproj src\WebAppDemo\
COPY packages.config src\WebAppDemo\

RUN nuget.exe restore WebAppDemo.sln

# Copy source codes
COPY ../../src src\

RUN MSBuild.exe "src\WebAppDemo\WebAppDemo.csproj" `
    /t:Build;WebPublish /m `
    /p:DebugType=None `
    /p:Configuration=Release `
    /p:WebPublishMethod=FileSystem `
    /p:TransformWebConfigEnabled=False `
    /p:LocalizedBuild=true `
    /p:AutoParameterizationWebConfigConnectionStrings=False `
    /p:MarkWebConfigAssistFilesAsExclude=false `
    /p:publishUrl="C:\Compiled" `
    /consoleloggerparameters:ErrorsOnly

FROM registry.nexdev.net:5050/docker/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019 AS base

WORKDIR C:\inetpub\wwwroot

COPY --from=builder C:\Compiled .

RUN dir .\bin\

ENTRYPOINT ["C:\\LogMonitor\\LogMonitor.exe", "C:\\ServiceMonitor.exe", "w3svc"]