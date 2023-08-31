# ----------------------------------------------------------
# VS-Code Development Docker Image
# ----------------------------------------------------------
#
FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu-22.04 as base

# BASE ARGS
ARG INSTALL_ZSH="false"
ARG UPGRADE_PACKAGES="true"
ARG USE_MOBY="true"
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG vscode
# BASE ENV
ENV HOME=/home/${USERNAME}
ENV DOCKER_BUILDKIT=1

RUN if [[ -z "$vscode" ]] ; then printf "\nERROR: This Dockerfile needs to be built with VS Code !" && exit 1; else printf "VS Code is detected: $vscode"; fi

RUN mkdir -p $HOME/.vscode-server/extensions \
    && chown -R $USERNAME ${HOME}/.vscode-server

RUN apt-get update -y && apt-get install -y software-properties-common wget ca-certificates build-essential g++ libx11-dev libxkbfile-dev libsecret-1-dev python-is-python3 xsel make maven apache2 && apt-get autoremove -y && apt-get clean -y

FROM base as op_system

SHELL [ "/bin/bash", "-c" ]
# since we're starting non-interactive shell,
# we wil need to tell bash to load .bashrc manually
ENV BASH_ENV ~/.bashrc
# needed by volta() function
ENV VOLTA_HOME ${HOME}/.volta
# make sure packages managed by volta will be in PATH
ENV PATH $VOLTA_HOME/bin:$PATH

RUN mkdir ${VOLTA_HOME}
RUN chown -R ${USERNAME}:$(id -gn ${USERNAME}) ${VOLTA_HOME}
RUN wget https://github.com/ko1nksm/shdotenv/releases/latest/download/shdotenv --directory-prefix $HOME/bin/shdotenv
RUN chown -R ${USERNAME}:$(id -gn ${USERNAME}) $HOME/bin/shdotenv


RUN groupadd docker
RUN usermod -aG docker ${USERNAME}
# PACKAGING
FROM op_system as packaging

ARG BUILD_DATE
ARG RELEASE_VERSION

LABEL org.opencontainers.image.created = ${BUILD_DATE}
LABEL org.opencontainers.image.version = ${RELEASE_VERSION}

# # Setting the ENTRYPOINT to docker-init.sh will configure non-root access to
# # the Docker socket if "overrideCommand": false is set in devcontainer.json.
# # The script will also execute CMD if you need to alter startup behaviors.
ENTRYPOINT [ "/usr/local/share/docker-init.sh" ]
CMD [ "sleep", "infinity" ]