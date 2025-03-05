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

RUN $ErrorActionPreference = 'Stop'; \
    $ProgressPreference = 'SilentlyContinue'; \
    New-Item -Path 'C:\opt\vc' -ItemType Directory -Force; \
    Invoke-WebRequest -Uri "https://winworldpc.com/download/c39228e2-80ba-715d-2411-c3a4c2a90f70/from/c39ac2af-c381-c2bf-1b25-11c3a4e284a2" \
        -OutFile "C:\opt\vc\vc.7z"; \
    & 'C:\Program Files\7-Zip\7z.exe' x 'C:\opt\vc\vc.7z' -o'C:\opt\vc' -y -r; \
    New-Item -Path 'C:\opt\vc\setup' -ItemType Directory -Force; \
    & 'C:\Program Files\7-Zip\7z.exe' x 'C:\opt\vc\Microsoft Visual C++ 6.0 Standard.iso' -o'C:\opt\vc\setup' -y -r; \
    Remove-Item -Path 'C:\opt\vc\*.iso','C:\opt\vc\*.txt','C:\opt\vc\*.7z' -Force; \
    Move-Item -Path 'C:\opt\vc\setup\VC98\*' -Destination 'C:\opt\vc\' -Force

# Rename and clean up directories
RUN $paths = @( \
    @{Old='C:\opt\vc\CRT\SRC\ALGRITHM'; New='C:\opt\vc\CRT\SRC\ALGORITHM'}, \
    @{Old='C:\opt\vc\CRT\SRC\FCTIONAL'; New='C:\opt\vc\CRT\SRC\FUNCTIONAL'}, \
    @{Old='C:\opt\vc\CRT\SRC\MAKEFILE'; New='C:\opt\vc\CRT\SRC\EXT_MKF'}, \
    @{Old='C:\opt\vc\CRT\SRC\STDXCEPT'; New='C:\opt\vc\CRT\SRC\STDEXCEPT'}, \
    @{Old='C:\opt\vc\CRT\SRC\STREAMBF'; New='C:\opt\vc\CRT\SRC\STREAMBUF'}, \
    @{Old='C:\opt\vc\CRT\SRC\STRSTREM'; New='C:\opt\vc\CRT\SRC\STRSTREAM'}, \
    @{Old='C:\opt\vc\CRT\SRC\XCEPTION'; New='C:\opt\vc\CRT\SRC\EXCEPTION'}, \
    @{Old='C:\opt\vc\INCLUDE\ALGRITHM'; New='C:\opt\vc\INCLUDE\ALGORITHM'}, \
    @{Old='C:\opt\vc\INCLUDE\FCTIONAL'; New='C:\opt\vc\INCLUDE\FUNCTIONAL'}, \
    @{Old='C:\opt\vc\INCLUDE\STDXCEPT'; New='C:\opt\vc\INCLUDE\STDEXCEPT'}, \
    @{Old='C:\opt\vc\INCLUDE\STREAMBF'; New='C:\opt\vc\INCLUDE\STREAMBUF'}, \
    @{Old='C:\opt\vc\INCLUDE\STRSTREM'; New='C:\opt\vc\INCLUDE\STRSTREAM'}, \
    @{Old='C:\opt\vc\INCLUDE\XCEPTION'; New='C:\opt\vc\INCLUDE\EXCEPTION'} \
); \
foreach ($path in $paths) { \
    if (Test-Path $path.Old) { \
        Move-Item -Path $path.Old -Destination $path.New -Force; \
    } \
}

# Set environment variables
ENV PATH="C:\opt\vc\BIN;%PATH%"
ENV INCLUDE="C:\opt\vc\INCLUDE;%INCLUDE%"
ENV LIB="C:\opt\vc\LIB;%LIB%"

WORKDIR C:\\opt\\vc