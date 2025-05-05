ARG ROS_DISTRO=humble
FROM ros:${ROS_DISTRO}-ros-base AS base

ARG ROS_DISTRO

ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8
ENV DEBIAN_FRONTEND=noninteractive
ENV TERM=xterm-256color


RUN apt-get update && apt-get install -y --no-install-recommends\
    wget \
    curl \
    terminator \
    iputils-ping \
    openssh-server \
    iproute2 \
    python3-pip \
    nano \
    build-essential \
    cmake \
    git \
    python3-colcon-common-extensions \
    python3-colcon-mixin \
    python3-flake8 \
    python3-vcstool \
    freeglut3-dev \
    lsb-release gnupg \
    libglew-dev \
    libqt5opengl5-dev \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN pip3 install --no-cache-dir --upgrade pip\
    argcomplete \
    flake8-blind-except \
    flake8-builtins \
    flake8-comprehensions \
    flake8-deprecated \
    flake8-return \
    flake8-length \
    flake8-todo \
    flake8-quotes \
    black \
    mypy \
    prospector \
    pylint \
    numpy \
    opencv-python \
    pydantic==1.10.14 \
    panda \
    matplotlib \
    setuptools==58.3.0 \
    rosbags     

RUN apt-get update && apt-get install -y --no-install-recommends\
    ros-$ROS_DISTRO-rqt \
    ros-$ROS_DISTRO-rqt-common-plugins \
    ros-$ROS_DISTRO-rqt-robot-dashboard \
    ros-$ROS_DISTRO-rqt-robot-steering \
    ros-$ROS_DISTRO-rqt-tf-tree \
    ros-$ROS_DISTRO-rqt-tf-tree \
    ros-$ROS_DISTRO-image-transport-plugins \
    ros-$ROS_DISTRO-compressed-image-transport \
    ros-$ROS_DISTRO-cv-bridge \
    ros-$ROS_DISTRO-rosbag2-py \
    ros-$ROS_DISTRO-usb-cam \
    ros-$ROS_DISTRO-ur \
    ros-$ROS_DISTRO-ur-msgs \
    ros-$ROS_DISTRO-ur-client-library \
    ros-$ROS_DISTRO-ur-description \
    ros-$ROS_DISTRO-ros2-control \
    ros-$ROS_DISTRO-ros2-controllers \
    ros-$ROS_DISTRO-realtime-tools \
    ros-$ROS_DISTRO-kinematics-interface \
    ros-$ROS_DISTRO-control-msgs \
    ros-$ROS_DISTRO-rviz2 \
    ros-$ROS_DISTRO-rmw-cyclonedds-cpp \
    ros-$ROS_DISTRO-ros-testing \
    ros-$ROS_DISTRO-ros2bag \
    ros-$ROS_DISTRO-ign-ros2-control \
    ros-$ROS_DISTRO-moveit \
    ros-$ROS_DISTRO-moveit-servo \
    ros-$ROS_DISTRO-pilz-industrial-motion-planner \
    ros-$ROS_DISTRO-ros-ign-bridge \
    ros-$ROS_DISTRO-ros-ign-gazebo \
    ros-$ROS_DISTRO-ament-clang-format \
    ros-$ROS_DISTRO-moveit-visual-tools \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# niceness permissions (for ur driver use without Prempt-RT)
RUN echo "* - nice -15" >> /etc/security/limits.conf

FROM base AS ka

RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4a1.4/libk4a1.4_1.4.1_amd64.deb > /tmp/libk4a1.4_1.4.1_amd64.deb
RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4a1.4-dev/libk4a1.4-dev_1.4.1_amd64.deb > /tmp/libk4a1.4-dev_1.4.1_amd64.deb
RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4abt1.1/libk4abt1.1_1.1.2_amd64.deb > /tmp/libk4abt1.1_1.1.2_amd64.deb
RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/libk4abt1.1-dev/libk4abt1.1-dev_1.1.2_amd64.deb > /tmp/libk4abt1.1-dev_1.1.2_amd64.deb
RUN curl -sSL https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/k/k4a-tools/k4a-tools_1.4.1_amd64.deb > /tmp/k4a-tools_1.4.1_amd64.deb
RUN echo 'libk4a1.4 libk4a1.4/accepted-eula-hash string 0f5d5c5de396e4fee4c0753a21fee0c1ed726cf0316204edda484f08cb266d76' | debconf-set-selections
RUN echo 'libk4abt1.1	libk4abt1.1/accepted-eula-hash	string	03a13b63730639eeb6626d24fd45cf25131ee8e8e0df3f1b63f552269b176e38' | debconf-set-selections
RUN dpkg -i /tmp/libk4a1.4_1.4.1_amd64.deb
RUN dpkg -i /tmp/libk4a1.4-dev_1.4.1_amd64.deb
RUN dpkg -i /tmp/libk4abt1.1_1.1.2_amd64.deb
RUN dpkg -i /tmp/libk4abt1.1-dev_1.1.2_amd64.deb
RUN curl -fSL http://archive.ubuntu.com/ubuntu/pool/universe/libs/libsoundio/libsoundio1_1.0.3-1_amd64.deb -o /tmp/libsoundio1.deb && \
    dpkg -i /tmp/libsoundio1.deb || apt-get install -y -f

# fix k4abtConfig.cmake Error: Azure Kinect SDK Version numbers contain exactly 3 components
RUN sed -i 's/find_dependency(k4a 1.4 REQUIRED)/find_dependency(k4a 1.4.1 REQUIRED)/' /usr/lib/cmake/k4abt/k4abtConfig.cmake

RUN pip3 install --no-cache-dir --upgrade pyk4a

FROM ka AS open3d

RUN pip3 install --no-cache-dir --upgrade open3d --ignore-installed PyYAML



FROM open3d AS build
RUN mkdir -p /dev_ws/src

WORKDIR /dev_ws

COPY . /dev_ws/src

# RUN pip3 install setuptools==58.3.0

# RUN apt-get update && rosdep install --from-paths src --ignore-src --skip-keys="azure_kinect_ros_driver" -r -y \
#     && apt-get clean && rm -rf /var/lib/apt/lists/*

FROM build AS bash

RUN ["/bin/bash", "-c", "source /opt/ros/$ROS_DISTRO/setup.bash && \
     colcon build --merge-install --symlink-install --cmake-args '-DCMAKE_BUILD_TYPE=Release' --packages-skip azure_kinect_ros_driver easy_handeye2 && \
     colcon build --merge-install --packages-select azure_kinect_ros_driver easy_handeye2"]


RUN ["/bin/bash", "-c", "cp /dev_ws/src/.docker/setup.bash /dev_ws/setup.bash && chmod 777 /dev_ws/setup.bash"]
RUN ["/bin/bash", "-c", "cp /dev_ws/src/.docker/entrypoint.bash / && chmod 777 /entrypoint.bash"]

ENTRYPOINT ["bash", "/entrypoint.bash"]

CMD ["bash"]