ARG ROS_DISTRO=humble

## our base image ##
FROM osrf/ros:${ROS_DISTRO}-desktop-full
ENV ROS_DISTRO=${ROS_DISTRO}
ENV CMAKE_PREFIX_PATH=/opt/ros/${ROS_DISTRO}/share:${CMAKE_PREFIX_PATH}
SHELL ["/bin/bash", "-c"]

# Add ubuntu user with same UID and GID as your host system, if it doesn't already exist
ARG USERNAME=ubuntu
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Install basic apt packages
#RUN apt-get update && apt-get upgrade -y 
RUN apt-get update -y
RUN apt install -y python3-colcon-common-extensions python3-vcstool \ 
 curl git libcanberra-gtk-module libcanberra-gtk3-module fuse3 libfuse2 libqt5svg5-dev \
 python3-pip python3-opencv python3-tk python3-pyqt5.qtwebengine

# Install additional Python modules
RUN pip3 install matplotlib transforms3d

# Use Cyclone DDS as middleware
RUN apt-get update && apt-get install -y --no-install-recommends \
 ros-${ROS_DISTRO}-rmw-cyclonedds-cpp
ENV RMW_IMPLEMENTATION=rmw_cyclonedds_cpp

# Create Colcon workspace with external dependencies
# RUN mkdir -p /kinova_ws/src
WORKDIR /gen3_ws/gen3_ws

RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /root/.bashrc

# git and install the ROS2 driver for kinova
RUN export COLCON_WS=/gen3_ws/gen3_ws && \
    mkdir -p $COLCON_WS/src && \
    cd $COLCON_WS && \
    git clone https://github.com/Kinovarobotics/ros2_kortex.git src/ros2_kortex && \
    vcs import src --skip-existing --input src/ros2_kortex/ros2_kortex.$ROS_DISTRO.repos && \
    vcs import src --skip-existing --input src/ros2_kortex/ros2_kortex-not-released.$ROS_DISTRO.repos && \
    vcs import src --skip-existing --input src/ros2_kortex/simulation.humble.repos

# Install dependencies
RUN rosdep install --ignore-src --from-paths src -y -r 
RUN apt-get update && apt-get install -y \
    ros-${ROS_DISTRO}-ament-cmake

# Build the workspace
RUN source /opt/ros/${ROS_DISTRO}/setup.bash && \
    colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release --parallel-workers 3
RUN echo 'source /gen3_ws/gen3_ws/install/setup.bash' >> ~/.bashrc
