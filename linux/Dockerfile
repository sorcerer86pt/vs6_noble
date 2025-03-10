FROM ubuntu:noble

# Instalar dependências necessárias
RUN dpkg --add-architecture i386 && apt update && apt install -y \
    wine wine32 wget p7zip-full nano cmake git

# Criar diretório e baixar o VC6
RUN mkdir -p /opt/vc && cd /opt/vc && \
    wget -O vc.7z https://github.com/ahaczewski/GeneralsToochain/releases/download/v1/VC6.7z && \
    7z x vc.7z && \
    mv VC6/* /opt/vc/ && \
    rm -rf VC6 vc.7z  # Limpeza dos arquivos desnecessários

# Verificar a instalação do Wine
RUN wine winver || true

# Definir diretório de trabalho e executar o script do VC6
WORKDIR /opt/vc
CMD wine cmd /k VCVARALL.BAT
