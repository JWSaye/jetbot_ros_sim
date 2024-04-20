# jetbot_ros
ROS2 nodes and Gazebo model for NVIDIA JetBot

## Prerequisites

Ensure you have ROS2 Foxy and Gazebo installed on your Ubuntu machine.

## Setup ROS2 Foxy

### Setup Sources

You will need to add the ROS 2 apt repository to your system.

First, ensure that the Ubuntu Universe repository is enabled:

```bash
sudo apt install software-properties-common
sudo add-apt-repository universe
```

Now add the ROS 2 GPG key with apt:

```bash
sudo apt update && sudo apt install curl -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
```

Then add the repository to your sources list:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```
### Install ROS 2 Packages

Update your apt repository caches after setting up the repositories:

```bash
sudo apt update
```

ROS 2 packages are built on frequently updated Ubuntu systems. It is always recommended that you ensure your system is up to date before installing new packages:

```bash
sudo apt upgrade
```

Now choose which level of ROS 2 to install from the updated packages:

- **Desktop Install (Recommended):** Includes ROS, RViz, demos, tutorials.

    ```bash
    sudo apt install ros-foxy-desktop python3-argcomplete
    ```

- ROS-Base Install (Bare Bones): Includes communication libraries, message packages, command line tools. No GUI tools.

    ```bash
    sudo apt install ros-foxy-ros-base python3-argcomplete
    ```

- Development Tools: Compilers and other tools to build ROS packages:

    ```bash
    sudo apt install ros-dev-tools
    ```

#### Environment Setup

To automatically source the ROS2 environment in each new shell session, add the sourcing command to your ~/.bashrc file:

```bash
echo "source /opt/ros/foxy/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### Setup
Clone the repository and navigate into the project directory:
``` bash
git clone https://github.com/dusty-nv/jetbot_ros
cd jetbot_ros
```

### Launch Gazebo

``` bash
ros2 launch jetbot_ros gazebo_world.launch.py
```

### Test Teleop

``` bash
ros2 launch jetbot_ros teleop_keyboard.launch.py
```

The keyboard controls are as follows:

```
w/x:  increase/decrease linear velocity
a/d:  increase/decrease angular velocity

space key, s:  force stop
```

Press Ctrl+C to quit.

### Data Collection

``` bash
ros2 launch jetbot_ros data_collection.launch.py
```

It's recommended to view the camera feed in Gazebo by going to `Window -> Topic Visualization -> gazebo.msgs.ImageStamped` and selecting the `/gazebo/default/jetbot/camera_link/camera/image` topic.

Drive the robot and press the `C` key to capture an image.  Then annotate that image in the pop-up window by clicking the center point of the path.  Repeat this all the way around the track.  It's important to also collect data of when the robot gets off-course (i.e. near the edges of the track, or completely off the track).  This way, the JetBot will know how to get back on track.

Press Ctrl+C when you're done collecting data to quit.

### Train Navigation Model

Navigate to the directory containing the training script and run the following command, substituting the path of your dataset:

``` bash
cd /workspace/src/jetbot_ros/jetbot_ros/dnn
python3 train.py --data /workspace/src/jetbot_ros/data/datasets/20211018-160950/
```

### Run Navigation Model

After training, launch the navigation model to enable the JetBot to autonomously navigate around the track:

``` bash
ros2 launch jetbot_ros nav_model.launch.py model:=/workspace/src/jetbot_ros/data/models/202106282129/model_best.pth
```

> note:  to reset the position of the robot in the Gazebo environment, press `Ctrl+R`

<a href="https://youtu.be/gok9pvUzZeY" target="_blank"><img src=https://github.com/dusty-nv/jetbot_ros/raw/dev/docs/images/jetbot_gazebo_sim_video.jpg width="750"></a>

