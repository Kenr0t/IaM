# # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #
#                                   BASIC CONFIG                                    #
# # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #

FROM openjdk:17-jdk-bullseye

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG PYTHON_VERSION=3.10.8

ENV DEBIAN_FRONTEND=noninteractive

# Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
RUN groupadd --gid $USER_GID $USERNAME &&\
    useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME

# Add sudo support.
RUN apt-get update && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# SSH config
RUN mkdir /home/${USERNAME}/.ssh &&\
    echo "Host *\n\tIdentitiesOnly=yes\n\tServerAliveInterval=25" > /home/${USERNAME}/.ssh/config &&\
    chown 1000:1000 -R /home/${USERNAME}/.ssh/


# # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #
#                           INSTALLING PACKAGES WITH APT-GET                        #
# # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #
RUN apt-get update -y && apt-get upgrade -y
RUN apt-get install -y --no-install-recommends \
    sudo git zsh curl wget apt-utils dialog \
    gcc g++ build-essential cmake build-essential llvm \
    zip unzip ca-certificates locales openssh-client

# Installing Locales
RUN echo "LC_ALL=en_US.UTF-8" >> /etc/environment &&\
    echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen &&\
    echo "LANG=en_US.UTF-8" > /etc/locale.conf &&\
    locale-gen en_US.UTF-8

# Cleanup
RUN apt-get autoremove -y && \
    apt-get clean autoclean && \
    rm -rf /var/lib/apt/lists/*


# # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #
#                            INSTALLING FROM OTHER SOURCES                          #
# # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #
# Installing Starship prompt
RUN curl -fsSL https://starship.rs/install.sh | sh -s -- --yes &&\
    echo 'eval "$(starship init bash)"' >> /home/$USERNAME/.bashrc

# Installing SDKMAN
RUN curl -s "https://get.sdkman.io" | bash

# Install Gradle 8.2
SHELL ["/bin/bash", "-c"]
RUN source "/root/.sdkman/bin/sdkman-init.sh" && \
    sdk install gradle 7.5.1


# re-enable dialog mode for apt-get
ENV DEBIAN_FRONTEND=dialog
