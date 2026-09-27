FROM ros:humble-ros-base-jammy

ARG USERNAME=dev
ARG UID=1000
ARG GID=1000

SHELL ["/bin/bash", "-c"]

# 1. Install dependensi dasar & sudo
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3-colcon-common-extensions \
    python3-pip \
    python3-rosdep \
    ros-humble-rviz2 \
    ros-humble-rqt-graph \
    ros-humble-demo-nodes-cpp \
    ros-humble-demo-nodes-py \
    git nano iputils-ping net-tools sudo \
    && rm -rf /var/lib/apt/lists/*

# 2. Buat user & berikan akses sudo (tanpa password)
RUN groupadd -g ${GID} ${USERNAME} 2>/dev/null || true && \
    useradd -m -u ${UID} -g ${GID} -s /bin/bash ${USERNAME} && \
    echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/${USERNAME} && \
    chmod 0440 /etc/sudoers.d/${USERNAME}

# 3. Buat folder workspace dan berikan kepemilikan ke user non-root
RUN mkdir -p /ws && chown -R ${USERNAME}:${GID} /ws

# 4. Copy entrypoint sebagai root
COPY docker/entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# 5. Pindah ke user non-root & direktori kerja
USER ${USERNAME}
WORKDIR /ws

# 6. Setup environment di .bashrc dan jalankan rosdep update
RUN echo "source /opt/ros/humble/setup.bash" >> /home/${USERNAME}/.bashrc && \
    echo "[ -f /ws/install/setup.bash ] && source /ws/install/setup.bash" >> /home/${USERNAME}/.bashrc && \
    rosdep update

ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]
