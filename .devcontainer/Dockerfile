# Based on https://github.com/devcontainers/templates/tree/main/src/ruby, with further customizations for IETF I-D
FROM mcr.microsoft.com/devcontainers/ruby:1-3.3-bullseye
RUN apt-get update \
    && apt-get install -y python3.9 python3-pip python3-venv
RUN pip3 install --upgrade xml2rfc iddiff svgcheck intervaltree pip wheel setuptools #just to get everything updated container-build-time
RUN mkdir -p ~/.bundle/cache && chmod +t -R ~/.bundle/cache && gem install kdwatch kramdown-mermaid

#Install mermaid-ascii
# # Latest 
# RUN curl -s https://api.github.com/repos/AlexanderGrooff/mermaid-ascii/releases/latest \
#     | grep "browser_download_url.*mermaid-ascii" | grep "$(uname)_$(uname -m)" | cut -d: -f2,3 | tr -d \" \
#     | wget -q -i- -O- | tar -xvz -f- -C /usr/bin -- mermaid-ascii
#0.6.0
RUN wget -q -O- https://github.com/AlexanderGrooff/mermaid-ascii/releases/download/0.6.0/mermaid-ascii_Linux_x86_64.tar.gz \
    | tar -xvz -f- -C /usr/bin -- mermaid-ascii
