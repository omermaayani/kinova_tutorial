ARG ROS_DISTRO=humble

## our base image ##
FROM osrf/ros:${ROS_DISTRO}-desktop-full
ENV ROS_DISTRO=${ROS_DISTRO}
SHELL ["/bin/bash", "-c"]

# Add ubuntu user with same UID and GID as your host system, if it doesn't already exist
ARG USERNAME=ubuntu
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Install basic apt packages
RUN apt-get update && apt-get install -y --no-install-recommends \
 curl git libcanberra-gtk-module libcanberra-gtk3-module fuse3 libfuse2 libqt5svg5-dev \
 python3-pip python3-opencv python3-tk python3-pyqt5.qtwebengine

# Install additional Python modules
RUN pip3 install matplotlib transforms3d

# Use Cyclone DDS as middleware
RUN apt-get update && apt-get install -y --no-install-recommends \
 ros-${ROS_DISTRO}-rmw-cyclonedds-cpp
ENV RMW_IMPLEMENTATION=rmw_cyclonedds_cpp

# Create Colcon workspace with external dependencies
RUN mkdir -p /kinova_ws/src
WORKDIR /kinova_ws/src

RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /root/.bashrc

