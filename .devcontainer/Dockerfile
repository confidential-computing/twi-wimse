# Based on https://github.com/devcontainers/templates/tree/main/src/ruby, with further customizations for IETF I-D
FROM mcr.microsoft.com/devcontainers/ruby:1-3.3-bullseye
RUN apt-get update \
    && apt-get install -y python3.9 python3-pip python3-venv
RUN pip3 install --upgrade xml2rfc iddiff svgcheck intervaltree pip wheel setuptools #just to get everything updated container-build-time
RUN mkdir -p ~/.bundle/cache && chmod +t -R ~/.bundle/cache && gem install kdwatch
