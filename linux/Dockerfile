FROM debian:12-slim

WORKDIR /build

# Install dependencies, add i386 architecture, add Wine repository & install Wine in one RUN
RUN apt-get update && \
    apt-get install -y wget gpg unzip git && \
    dpkg --add-architecture i386 && \
    mkdir -pm755 /etc/apt/keyrings && \
    wget -O - https://dl.winehq.org/wine-builds/winehq.key | gpg --dearmor -o /etc/apt/keyrings/winehq-archive.key && \
    wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/oracular/winehq-oracular.sources && \
    apt-get update && \
    apt-get install --install-recommends -y winehq-stable && \
    rm -rf /var/lib/apt/lists/*

# Download and install Windows tools in one RUN block and remove archives afterward
WORKDIR /build/tools
RUN wget https://github.com/Kitware/CMake/releases/download/v3.31.6/cmake-3.31.6-windows-x86_64.zip && \
    unzip cmake-3.31.6-windows-x86_64.zip -d /build/tools/ && \
    mv /build/tools/cmake-3.31.6-windows-x86_64 /build/tools/cmake && \
    wget https://github.com/git-for-windows/git/releases/download/v2.49.0-rc1.windows.1/MinGit-2.49.0-rc1-64-bit.zip && \
    unzip MinGit-2.49.0-rc1-64-bit.zip -d /build/tools/ && \
    mv /build/tools/cmd /build/tools/git && \
    wget https://github.com/itsmattkc/MSVC600/archive/refs/heads/master.zip && \
    unzip master.zip -d /build/tools && \
    mv /build/tools/MSVC600-master/ /build/tools/vs6 && \
    rm -f *.zip

WORKDIR /build

# Set environment variables in one ENV instruction
ENV WINEDEBUG=-all \
    WINEARCH=win64 \
    WINEPREFIX=/build/prefix64 \
    TMP="Z:\\build\\tmp" \
    TEMP="Z:\\build\\tmp" \
    TEMPDIR="Z:\\build\\tmp" \
    VS="Z:\\build\\tools\\vs6" \
    MSVCDir="Z:\\build\\tools\\vs6\\vc98" \
    WINEPATH="C:\\windows\\system32;Z:\\build\\tools\\vs6\\vc98\\bin;Z:\\build\\tools\\vs6\\vc98\\lib;Z:\\build\\tools\\vs6\\vc98\\include;Z:\\build\\tools\\vs6\\common\\Tools;Z:\\build\\tools\\vs6\\common\\MSDev98\\bin" \
    LIB="Z:\\build\\tools\\vs6\\vc98\\Lib;Z:\\build\\tools\\vs6\\vc98\\MFC\\Lib;Z:\\build\\cnc\\build\\vc6" \
    INCLUDE="Z:\\build\\tools\\vs6\\vc98\\ATL\\INCLUDE;Z:\\build\\tools\\vs6\\vc98\\INCLUDE;Z:\\build\\tools\\vs6\\vc98\\MFC\\INCLUDE;Z:\\build\\tools\\vs6\\vc98\\Include" \
    CC="Z:\\build\\tools\\vs6\\vc98\\bin\\CL.exe" \
    CXX="Z:\\build\\tools\\vs6\\vc98\\bin\\CL.exe" \
    GIT_VERSION_STRING="2.49.0"

# Setup Wine prefix and create the temporary folder
RUN mkdir -p /build/tmp && wineboot
