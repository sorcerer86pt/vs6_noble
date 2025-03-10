ARG 7ZIP_VERSION=2409

# Stage 1: Download 7-Zip
FROM mcr.microsoft.com/windows/servercore:ltsc2025 AS download
ARG 7ZIP_VERSION

SHELL ["cmd", "/S", "/C"]

RUN mkdir C:\Temp

SHELL ["powershell", "-Command"]

RUN $ErrorActionPreference = 'Stop'; \
    $ProgressPreference = 'SilentlyContinue'; \
    Invoke-WebRequest -Uri "https://www.7-zip.org/a/7z2409-x64.exe" -OutFile "C:\Temp\7zip-installer.exe"; \
    Start-Process -FilePath "C:\Temp\7zip-installer.exe" -ArgumentList '/S' -Wait

# Stage 2: Build VC++ 6.0 environment
FROM mcr.microsoft.com/windows/servercore:ltsc2025 AS build

# Copy 7-Zip from previous stage
COPY --from=download ["/Program Files/7-Zip", "/Program Files/7-Zip"]

SHELL ["powershell", "-Command"]

# Install GIT
ENV GIT_ZIP_URL https://github.com/git-for-windows/git/releases/download/v2.48.1.windows.1/MinGit-2.48.1-64-bit.zip  
RUN Invoke-WebRequest -Uri  $env:GIT_ZIP_URL -OutFile 'git.zip'; \
    Expand-Archive -Path 'git.zip' -DestinationPath 'C:\\git'; \
    Remove-Item git.zip -Force; \
    $env:PATH = 'C:\\git\\cmd;C:\\git\\bin;C:\\git\\usr\\bin;{0}' -f $env:PATH; \
    [Environment]::SetEnvironmentVariable('PATH', $env:PATH, [EnvironmentVariableTarget]::Machine); \
    Write-Host 'Completed git install.'

RUN $ErrorActionPreference = 'Stop'; \
    $ProgressPreference = 'SilentlyContinue'; \
    New-Item -Path 'C:\opt\vc' -ItemType Directory -Force; \
    Invoke-WebRequest -Uri "https://github.com/ahaczewski/GeneralsToochain/releases/download/v1/VC6.7z" \
        -OutFile "C:\opt\vc\vc.7z"; \
    & 'C:\Program Files\7-Zip\7z.exe' x 'C:\opt\vc\vc.7z' -o'C:\Program Files (x86)\Microsoft Visual Studio\' -y -r;

# Install CMake silently
ENV CMAKE_URL https://github.com/Kitware/CMake/releases/download/v3.31.6/cmake-3.31.6-windows-x86_64.msi
RUN Invoke-WebRequest -Uri $env:CMAKE_URL -OutFile 'CMake.msi'; \
    Start-Process msiexec.exe -ArgumentList '/i CMake.msi /quiet /norestart' -NoNewWindow -Wait; \
    Remove-Item CMake.msi -Force; \
    Write-Host 'Completed CMake install.'


WORKDIR C:\\Program Files (x86)\\Microsoft Visual Studio\\