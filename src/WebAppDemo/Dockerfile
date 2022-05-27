# escape=`

FROM registry.nexdev.net:5050/nexcel/nexops/rocketor/buildservice:base AS builder

SHELL ["cmd", "/S", "/C"]

WORKDIR /build

# Copy files *.sln, *.csproj to restore nuget packages
COPY *.sln .
COPY src\WebAppDemo\*.csproj src\WebAppDemo\
COPY src\WebAppDemo\packages.config src\WebAppDemo\

RUN C:\Users\ContainerAdministrator\AppData\Roaming\NuGet\nuget.exe restore WebAppDemo.sln

# Copy source codes
COPY src\WebAppDemo\ src\WebAppDemo\

RUN MSBuild.exe ".\src\WebAppDemo\WebAppDemo.csproj" /t:Build;WebPublish /m /p:Configuration=Release;WebPublishMethod=FileSystem;TransformWebConfigEnabled=False;LocalizedBuild=true;AutoParameterizationWebConfigConnectionStrings=False;MarkWebConfigAssistFilesAsExclude=false;DeleteExistingFiles=False;publishUrl="C:\Compiled" /consoleloggerparameters:ErrorsOnly

FROM registry.nexdev.net:5050/docker/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019 AS base

WORKDIR C:\inetpub\wwwroot

COPY --from=builder C:\Compiled .

ENTRYPOINT ["C:\\LogMonitor\\LogMonitor.exe", "C:\\ServiceMonitor.exe", "w3svc"]