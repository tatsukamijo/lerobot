FROM nvidia/cuda:12.4.1-base-ubuntu22.04

# Arguments for customization
ARG PYTHON_VERSION=3.10
ARG DEBIAN_FRONTEND=noninteractive

# Configure environment
ENV MUJOCO_GL="egl"

# Install Python 3.10 and pip
RUN apt-get update && apt-get install -y --no-install-recommends \
        curl \
        software-properties-common && \
    add-apt-repository ppa:deadsnakes/ppa && \
    apt-get update && apt-get install -y --no-install-recommends \
        python${PYTHON_VERSION} \
        python${PYTHON_VERSION}-distutils && \
    update-alternatives --install /usr/bin/python3 python3 /usr/bin/python${PYTHON_VERSION} 1 && \
    update-alternatives --set python3 /usr/bin/python${PYTHON_VERSION} && \
    curl -sS https://bootstrap.pypa.io/get-pip.py | python${PYTHON_VERSION} && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install essential tools
RUN apt-get update && \
    apt-get install -y \
        wget \
        git \
        vim \
        tmux \
        lsb-release \
        gnupg \
        avahi-autoipd \
        avahi-daemon \
        avahi-utils

# Install essential dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        libglib2.0-0 \
        libgl1-mesa-glx \
        libegl1-mesa \
        libxkbcommon-x11-0 \
        vulkan-tools \
        ffmpeg \
        speech-dispatcher \
        python${PYTHON_VERSION}-dev \
        python${PYTHON_VERSION}-venv && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install neovim
RUN curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux64.tar.gz && \
    rm -rf /opt/nvim && \
    tar -C /opt -xzf nvim-linux64.tar.gz && \
    echo 'export PATH="$PATH:/opt/nvim-linux64/bin"' >> /root/.bashrc && \
    git clone https://github.com/tatsukamijo/kickstart.nvim.git "${XDG_CONFIG_HOME:-$HOME/.config}"/nvim

# Create Python symlink
RUN ln -sf /usr/bin/python${PYTHON_VERSION} /usr/bin/python

# Copy the helper scripts to the working directory
COPY ./docker/scripts/. /root/scripts/
COPY ./docker/config/. /root/
RUN chmod -R +x /root/scripts/*

# Optional: Uncomment to install project-specific Python dependencies
# RUN pip install --no-cache-dir ".[test, aloha, xarm, pusht, dynamixel]"

# Setup the Bash shell environment with '/root/.bashrc'
RUN echo "source /root/scripts/initialize-bash-shell.sh" >> /root/.bashrc

# Force color prompt in terminal
RUN sed -i 's/#force_color_prompt=yes/force_color_prompt=yes/' /root/.bashrc

# Clean up
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /root

# Set the same permission for the host and the container
ENV GROUPNAME=weblab
ENV USERNAME=weblab
ENV UID=1000
ENV GID=1000

RUN groupadd -g $GID $GROUPNAME && \
    useradd -m -u $UID -g $GID $USERNAME

COPY --chown=$UID:$GID . /root/work

ENTRYPOINT []

CMD ["/bin/bash"]

