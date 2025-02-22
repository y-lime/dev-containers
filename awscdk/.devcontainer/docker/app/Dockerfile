ARG UBUNTU_VERSION=24.04

FROM ubuntu:${UBUNTU_VERSION} 

ARG LOCALE=ja_JP.UTF-8
ARG USER_NAME=dev
ARG USER_GROUP_NAME=dev
ARG USER_UID=1001
ARG USER_GID=1001

SHELL ["/bin/bash", "-c"]

RUN apt-get update \
    && apt-get install -y sudo unzip zip vim tar less iputils-ping curl jq git emacs

# Use Japanese Language
RUN apt-get install -y locales \
    && locale-gen ${LOCALE}

ENV LANG ${LOCALE}

RUN groupadd --gid ${USER_GID} ${USER_GROUP_NAME} \
    && useradd --uid ${USER_UID} --gid ${USER_GID} --shell /bin/bash --create-home -m ${USER_NAME} \
    && echo "${USER_GROUP_NAME}	ALL=(ALL:ALL) NOPASSWD: ALL" >> /etc/sudoers.d/${USER_GROUP_NAME} \
    && chmod 0440 /etc/sudoers.d/${USER_GROUP_NAME}

# Switch to non-root user.
USER ${USER_NAME}
WORKDIR /home/${USER_NAME}

# Install Node.js.
RUN curl -sSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - \
    && deps="nodejs" \
    && sudo apt-get update && sudo apt-get install -y ${deps} --no-install-recommends \
    && sudo rm -rf /var/lib/apt/lists/* 

# Install global Node.js dependencies.
RUN sudo npm install --location=global npm \
    && sudo npm install --location=global typescript \
    && sudo npm install --location=global aws-cdk-lib \
    && sudo npm install --location=global aws-cdk

# Install AWS CLI v2 and enable AWS CLI auto-completion.
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && sudo ./aws/install \
    && rm ./awscliv2.zip \
    && rm -rf ./aws \
    && echo "" >> ~/.bashrc \
    && echo "# Enables AWS CLI auto-completion." >> ~/.bashrc \
    && echo "export PATH=/usr/local/bin:$PATH" >> ~/.bashrc \
    && echo "complete -C '/usr/local/bin/aws_completer' aws" >> ~/.bashrc