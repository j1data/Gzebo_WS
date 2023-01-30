FROM ubuntu:20.04
 
SHELL ["/bin/bash", "-c"]

# ~~~~~~~~~~~~~~~~ inside root user ~~~~~~~~~~~~~~~~~

# Minimal setup
RUN apt-get update \
 && apt-get install -y locales lsb-release
ARG DEBIAN_FRONTEND=noninteractive
RUN dpkg-reconfigure locales
 
# Install ROS Noetic
RUN apt-cache policy | grep universe
RUN apt-get install -y software-properties-common
RUN add-apt-repository universe
RUN apt-get install -y wget
RUN apt update -y && apt install -y curl gnupg2 lsb-release

#add gazebo repo
RUN sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
RUN wget https://packages.osrfoundation.org/gazebo.key -O - | apt-key add -

#install ros
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null
RUN apt-get update \
 && apt-get upgrade -y \
 && apt-get install -y --no-install-recommends ros-foxy-desktop python3-argcomplete
RUN apt-get install -y --no-install-recommends python3-rosdep
RUN rosdep init \
 && rosdep fix-permissions \
 && rosdep update

#install gazebo
RUN apt-get install -y gazebo11
RUN apt-get install -y libgazebo11-dev

#build nav2
RUN apt-get install -y python3-colcon-common-extensions

RUN apt-get install -y git
RUN mkdir -p -v /nav2_ws/src
RUN cd /nav2_ws/src \ 
 && git clone https://github.com/ros-planning/navigation2.git --branch foxy-devel

RUN apt-get install -y gcc g++
RUN apt-get install -y ros-foxy-navigation2 ros-foxy-nav2-bringup '~ros-foxy-turtlebot3-.*'

RUN source /opt/ros/foxy/setup.bash \
 && cd /nav2_ws  \
 && rosdep install -y -r -q --from-paths src --ignore-src --rosdistro foxy 
RUN cd /nav2_ws  \
 && source /opt/ros/foxy/setup.bash \
 && colcon build --symlink-install 

#install ros dev tools
RUN apt update && sudo apt install -y \
  libbullet-dev \
  python3-pip \
  python3-pytest-cov \
  ros-dev-tools

RUN pip install \
  argcomplete \
  flake8-blind-except \
  flake8-builtins \
  flake8-class-newline \
  flake8-comprehensions \
  flake8-deprecated \
  flake8-docstrings \
  flake8-import-order \
  flake8-quotes \
  pytest-repeat \
  pytest-rerunfailures \
  pytest

RUN apt install --no-install-recommends -y \
  libasio-dev \
  libtinyxml2-dev

RUN apt install --no-install-recommends -y \
  libcunit1-dev

RUN apt install ros-foxy-joint-state-publisher-gui -y
RUN apt install ros-foxy-xacro -y
RUN apt install ros-foxy-gazebo-ros-pkgs
RUN apt install ros-foxy-rqt-robot-steering
RUN apt install nano -y

# install pip libraries
#RUN pip install <name>


# ~~~~~~~~~~~~ from this point on in usr arc ~~~~~~~~~~~~~~
# not root
RUN useradd -ms /bin/bash arc
USER arc

#source necessary things
RUN echo "source /opt/ros/foxy/setup.bash" >> ~/.bashrc
RUN echo "source /nav2_ws/install/setup.bash" >> ~/.bashrc
RUN echo "source /usr/share/colcon_cd/function/colcon_cd.sh" >> ~/.bashrc
RUN echo "export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:/workspaces/Gzebo_WS/ros_ws/ros_ws/src/basic_mobile_robot/models" >> ~/.bashrc