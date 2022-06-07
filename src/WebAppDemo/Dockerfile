# escape=`
FROM registry.nexdev.net:5050/nexcel/nexops/rocketor/buildservice:base AS builder

ARG DEPLOY_ENV
ARG ROCKETOR_API

SHELL ["cmd", "/S", "/C"]

WORKDIR /build

# Copy files *.sln, *.csproj, packages.config to restore nuget packages
COPY *.sln .
COPY src\WebAppDemo\*.csproj src\WebAppDemo\
COPY src\WebAppDemo\packages.config src\WebAppDemo\

RUN nuget.exe restore WebAppDemo.sln

# Copy source codes
COPY src src\

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

WORKDIR C:\Compiled

# Change SP Version
RUN `
    powershell " `
    $ErrorActionPreference = 'Stop'; `
    $ProgressPreference = 'SilentlyContinue'; `
    $VerbosePreference = 'Continue'; `
    $spFile = Get-Item -Path 'App_Data\SPNames.%DEPLOY_ENV%.xml'; `
    $form = @{ `
        file = $spFile `
        redmineIds = '%REDMINE_IDS%' `
    }; `
    $response = Invoke-WebRequest -OutFile C:\sp-result.xml -Method 'POST' -Uri '%ROCKETOR_API%' -Form $form; `
    if($response.StatusCode == 200){ `
        xcopy C:\sp-result.xml 'App_Data\SPNames.%DEPLOY_ENV%.xml' /Y; `
    };"

# Transform config
RUN echo "%DEPLOY_ENV%" `
    && xcopy "Web.%DEPLOY_ENV%.config" "Web.config" /Y `
    && xcopy "App_Data\SPNames.%DEPLOY_ENV%.xml" "App_Data\SPNames.xml" /Y

FROM registry.nexdev.net:5050/docker/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019-utc7-new AS base

WORKDIR C:\inetpub\wwwroot

COPY --from=builder C:\Compiled .

RUN dir .\bin\

ENTRYPOINT ["C:\\LogMonitor\\LogMonitor.exe", "C:\\ServiceMonitor.exe", "w3svc"]