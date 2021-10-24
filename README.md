# AR Track Camera
This repository shows a basic implementation of the ROS ar_track_alvar library on Ubuntu 18.04 LTS using ROS Melodic and Logitech C525 USB Camera.

## Project Structure

1. [ROS Melodic Installation](#ROS-Melodic-Installation)
2. [Creating and Configuring a ROS Environment](#Creating-and-Configuring-a-ROS-Environment)
3. [Installing AR Tag Tracking library](#Installing-AR-Tag-Tracking-library)
4. [Running Launch Files](#Running-Launch-Files)

## ROS Melodic Installation

* [Ubuntu Configuration](#Ubuntu-Configuration)
* [ROS Installation](#ROS-Installation)
* [ROS Dependencies](#ROS-Dependencies)

### Ubuntu Configuration

Add the **ROS repository** to your Ubuntu/Debian **sources.list**:

```console=
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

Add the keys for accessing it:

```console=
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

At this point you should have an output like this:

```console
Executing: /tmp/apt-key-gpghome.WudTyznLyJ/gpg.1.sh --keyserver hkp://keyserver.ubuntu.com:80 --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
gpg: key F42ED6FBAB17C654: public key "Open Robotics <info@osrfoundation.org>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

Update the packages list:

```console=
sudo apt update
```

At this time, the output should be like the following:

```console
Hit:1 http://eu-west-1.ec2.archive.ubuntu.com/ubuntu bionic InRelease
Hit:2 http://eu-west-1.ec2.archive.ubuntu.com/ubuntu bionic-updates InRelease
Hit:3 http://eu-west-1.ec2.archive.ubuntu.com/ubuntu bionic-backports InRelease
Get:4 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]     
Get:5 http://packages.ros.org/ros/ubuntu bionic InRelease [4669 B]                              
Get:6 http://packages.ros.org/ros/ubuntu bionic/main amd64 Packages [569 kB]
Fetched 662 kB in 2s (406 kB/s)   
Reading package lists... Done
Building dependency tree       
Reading state information... Done
3 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

### ROS Installation

There are many options when it comes to ROS Installation, i.e., **~~ROS Base~~**, **~~ROS Desktop~~** and **ROS Desktop Full**. In this case we are going to choose the <mark>**ROS Full Desktop**</mark> option.

**ROS Desktop Full** installs everything of ROS Desktop option plus **2D/3D simulators** and **2D/3D perception**.

Install ROS Desktop Full:

```console=
sudo apt install ros-melodic-desktop-full
```

After running the command, you should have an output like this:

```console
0 upgraded, 485 newly installed, 0 to remove and 3 not upgraded.
Need to get 234 MB of archives.
After this operation, 1132 MB of additional disk space will be used.
Do you want to continue? [Y/n]

Setting up ros-melodic-nodelet-topic-tools (1.9.16-0bionic.20190601.015001) ...
Setting up ros-melodic-nodelet-core (1.9.16-0bionic.20190601.015433) ...
Setting up ros-melodic-roswtf (1.14.3-0bionic.20190601.014658) ...
Setting up ros-melodic-ros-comm (1.14.3-0bionic.20190601.015500) ...
Setting up ros-melodic-ros-core (1.4.1-0bionic.20190601.015718) ...
Setting up ros-melodic-ros-base (1.4.1-0bionic.20190808.193524) ...
Processing triggers for libgdk-pixbuf2.0-0:amd64 (2.36.11-2) ...
Processing triggers for libc-bin (2.27-3ubuntu1) ...

user@computer:~$
```
**Important! - Troubleshooting**

Note that due to the differences on the Linux distributions that support ROS Melodic, you could have some problems with the next section such as the following error:

```console=
user@computer:~$ rosdep: command not found
```

If that's the case, dont worry, just run this few commands:

```console=
sudo apt-get install python-pip
sudo pip install -U rosdep
``` 

or use the recommended method on Ubuntu:

```console=
sudo apt-get install python-rosdep
```

### ROS Dependencies

ROS has a client which is in charge of managing commands and dependencies, and it is called ``rosdep``.

Initialize ``rosdep``:

```console=
sudo rosdep init
```

The output would be the following:

```console
Wrote /etc/ros/rosdep/sources.list.d/20-default.list
Recommended: please run
```

Update ``rosdep``:

```console=
rosdep update
```

The output for running the previous command:

```console
reading in sources list data from /etc/ros/rosdep/sources.list.d
Hit https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/osx-homebrew.yaml
Hit https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/base.yaml
Hit https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/python.yaml
Hit https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/ruby.yaml
Hit https://raw.githubusercontent.com/ros/rosdistro/master/releases/fuerte.yaml
Query rosdistro index https://raw.githubusercontent.com/ros/rosdistro/master/index-v4.yaml
Skip end-of-life distro "ardent"
Skip end-of-life distro "bouncy"
Add distro "crystal"
Add distro "dashing"
Add distro "eloquent"
Skip end-of-life distro "groovy"
Skip end-of-life distro "hydro"
Skip end-of-life distro "indigo"
Skip end-of-life distro "jade"
Add distro "kinetic"
Skip end-of-life distro "lunar"
Add distro "melodic"
Add distro "noetic"
updated cache in /home/ubuntu/.ros/rosdep/sources.cache
```

## Creating and Configuring a ROS Environment

We have ROS and the dependencies manager installed. Let’s configure our environment. This is a very important step, once we have it done, working with ROS will be smooth.

ROS is installed at ``/opt/ros/<distro>`` (in this case ``/opt/ros/melodic``). In order to have ROS commands available, it’s needed to source the shell file inside of the installation folder. This is done like the following:

```console=
source /opt/ros/melodic/setup.bash
```

But.. considering we want to have it available in every terminal we open, we use to have a “shortcut”, which is adding this command to the file "``/home/<user>/.bashrc``". The ``.bashrc`` file is called every time a new terminal is opened, therefore, we won’t need to ``source`` ROS setup, since we have the instruction in this file. In order to add the command to the file, you can edit it manually using an editor of your preference or just execute the command below:

```console=
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
```

Now create a ROS Workspace:

```console=
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
```

Additionally, if you look in your current directory you should now have a 'build' and 'devel' folder. Inside the 'devel' folder you can see that there are now several setup.*sh files. Sourcing any of these files will overlay this workspace on top of your environment. To understand more about this see the general catkin documentation: [catkin](http://wiki.ros.org/catkin). Before continuing source your new setup.*sh file: 

```console=
source devel/setup.bash
```

To make sure your workspace is properly overlayed by the setup script, make sure ROS_PACKAGE_PATH environment variable includes the directory you're in.

```console=
echo $ROS_PACKAGE_PATH
```

The output would be the following:

```console=
/home/youruser/catkin_ws/src:/opt/ros/melodic/share
```

## Installing AR Tag Tracking library

Install the library:

```console=
sudo apt-get install ros-melodic-ar-track-alvar
```

Install the Kinect driver:

```console=
sudo apt-get install ros-melodic-freenect-launch
```

Now that we have the package, let’s make our own AR Tag! To do that, you will either want to make a folder to keep all of your tags, or navigate to an existing one you want to output the images to. Once there, you will run:

```console=
rosrun ar_track_alvar createMarker 0
```

You should see a new file named ``MarkerData_0.png`` in your current directory. You can easily open the image from your terminal by running:

```console=
eog MarkerData_0.png
```

You should get an image that looks like the following pop up on your screen. It should also be noted that AR Tags have different patterns based off of the number you have requested. A zero will always look like a zero, a one will always look like a one and so on. This is really useful if you tag an object for a robot to interact with with a specific number. So you can reliably, and repeatably select the object again.

<center><img src="https://realitybytesdotblog.files.wordpress.com/2017/06/ar_0.jpg" alt="zero-ar-tag"></center>

</br>

Another note on printing. You can select different sizes to create an AR Tag. SO if  you wanted one really big one or several small one depending on your application, you can have that! It’s just important to note what size you printed it, because that’ll be important later. The default size that these tags are printed at are 9 units x 9 units. So for example. If you wanted to output a 3 tag that was 5cmx5cm, the syntax would be:

```console=
rosrun ar_track_alvar createMarker -s 5 7
```

**Important!**

Due to differences in how you can format your printer/setup an image print or if you take a bunch of images and put them on a single page, it can cause some changes in the actual size of the tag. I would suggest that you still measure the printed tag just to be absolutely sure of the size of the tag, because that will be important to get an estimate of the tag’s position with respect to the camera. A tiny tag that isn’t known to be tiny to the software could think it’s really far away.

Clone the [repository](https://github.com/alainware/ar-track-kinect-ros) into the ``catkin_ws`` workspace:

```console=
git clone https://github.com/alainware/ar-track-kinect-ros
```

## Installing Additional Dependencies

1) In order to be able to use your USB camera with ROS, you need to install the following package called **usb_cam**:

```console=
sudo apt install ros-melodic-usb-cam
```

2) For listing media devices, install the following package called **v4l-utils**:

```console=
sudo apt-get install v4l-utils
```

3) Test the package typing the following command on the terminal:

```console=
v4l2-ctl --list-devices
```

4) For managing media devices and file formats, install the following package called **ffmpeg**:

```console=
sudo apt install ffmpeg
```

5) Test the package typing the following command in the terminal:

```console=
ffplay /dev/video2
```

6) Additionally, you will need to install the following packages:

```console=
sudo apt-get install ros-melodic-video-stream-opencv
```

```console=
sudo apt-get install ros-melodic-image-pipeline
```

7) Finally, update Ubuntu packages:
```console=
sudo apt-get update
```

## Creating a Custom Package


1) Open a terminal in catkin_ws/src

```console=
catkin_create_pkg ar_tag_rover std_msgs rospy
```

2) In your custom package ``ar_tag_rover``, create a new folder called ``launch``. Inside, create a file called ``camera.launch``. Copy the code below into it. It is a modified version of the camera.launch file from video_stream_opencv. Note that video_stream_provider may have to be changed to 1 or 2 if you are using an external camera. If you are using a virtual machine, you will need to enable the webcam under Devices>Webcam in the Virtual Box menu.

```htmlembedded=
<launch>
   <arg name="camera_name" default="camera" />
   <!-- video_stream_provider can be a number as a video device or a url of a video stream -->
   <arg name="video_stream_provider" default="2" />
   <!-- frames per second to query the camera for -->
   <arg name="fps" default="30" />
   <!-- frame_id for the camera -->
   <arg name="frame_id" default="camera_link" />
   <!-- By default, calibrations are stored to file://${ROS_HOME}/camera_info/${NAME}.yaml
   To use your own fill this arg with the corresponding url, e.g.:
   "file:///$(find your_camera_package)/config/your_camera.yaml" -->
    <arg name="camera_info_url" default="" />
   <!-- flip the image horizontally (mirror it) -->
   <arg name="flip_horizontal" default="false" />
   <!-- flip the image vertically -->
   <arg name="flip_vertical" default="false" />
    <!-- force width and height, 0 means no forcing -->
    <arg name="width" default="0"/>
    <arg name="height" default="0"/>
   <!-- if show a image_view window subscribed to the generated stream -->
 <arg name="visualize" default="true"/>

   
    <!-- images will be published at /camera_name/image with the image transports plugins (e.g.: compressed) installed -->
    <group ns="$(arg camera_name)">
     <node pkg="video_stream_opencv" type="video_stream" name="$(arg camera_name)_stream" output="screen"> 
      <remap from="camera" to="image_raw" />
      <param name="camera_name" type="string" value="$(arg camera_name)" />
         <param name="video_stream_provider" type="string" value="$(arg video_stream_provider)" />
         <param name="fps" type="int" value="$(arg fps)" />
         <param name="frame_id" type="string" value="$(arg frame_id)" />
         <param name="camera_info_url" type="string" value="$(arg camera_info_url)" />
         <param name="flip_horizontal" type="bool" value="$(arg flip_horizontal)" />
         <param name="flip_vertical" type="bool" value="$(arg flip_vertical)" />
         <param name="width" type="int" value="$(arg width)" />
         <param name="height" type="int" value="$(arg height)" />
     </node>

     <node if="$(arg visualize)" name="$(arg camera_name)_image_view" pkg="image_view" type="image_view">
      <remap from="image" to="image_raw" />
     </node>
 </group>

</launch>
```

3) Next, create the launch file that does the tracking. Again, this is a modified launch file from the ar_track_alvar package. Create a file called ``track.launch`` in your launch file folder and copy the following code inside it. Note that you will need to set the marker size. This is the length in centimeters of one side of the black part of an AR Tag.

```htmlembedded=
<launch>
 <arg name="marker_size" default="6.9" />
 <arg name="max_new_marker_error" default="0.08" />
 <arg name="max_track_error" default="0.2" />
 <arg name="cam_image_topic" default="/camera/image_raw" />
 <arg name="cam_info_topic" default="/camera/camera_info" />
 <arg name="output_frame" default="/camera_link" />
 

 <node name="ar_track_alvar" pkg="ar_track_alvar" type="individualMarkersNoKinect" respawn="false" output="screen">
  <param name="marker_size"           type="double" value="$(arg marker_size)" />
  <param name="max_new_marker_error"  type="double" value="$(arg max_new_marker_error)" />
  <param name="max_track_error"       type="double" value="$(arg max_track_error)" />
  <param name="output_frame"          type="string" value="$(arg output_frame)" />

  <remap from="camera_image"  to="$(arg cam_image_topic)" />
  <remap from="camera_info"   to="$(arg cam_info_topic)" />
 </node>
</launch>
```

4) You might only want to have to launch one file. This launch file simply calls the other two. Name this file as ``main.launch``.

```htmlembedded=
<launch>
 <include file="$(find ar_tag_rover)/launch/camera.launch" />
 <include file="$(find ar_tag_rover)/launch/track.launch" />
</launch>
```
## Running Launch Files

Run the following command in order to build the package that was created in the previous section:

```console=
catkin_make
```

Navigate to the folder where you created your custom package. You should be able to see a folder named ``launch``. Navigate into the ``launch`` folder and run the following commands in <mark>**two separate terminals**</mark>...

- Terminal 1:
Set the appropriate output frame so that the default ``/map`` frame maps to the ``camera_link`` camera output frame:
```console=
rosrun tf2_ros static_transform_publisher 0 0 0 0 0 0 1 /map /camera_link
```
- Terminal 2:
```console=
roslaunch main.launch
```

Open rviz with the following command:

```console=
rviz
```

Add TF to the data visualized on the left.


**You're done! :)**


## Reference Links
* [How to Install ROS on Ubuntu 18.04](https://www.theconstructsim.com/how-to-install-ros-on-ubuntu/)
* [Tracking AR tags in ROS](https://varunagrawal.github.io/2017/10/23/ar-track/)
* [Installing and Configuring Your ROS Environment](http://wiki.ros.org/ROS/Tutorials/InstallingandConfiguringROSEnvironment)
* [Detecting and Tracking AR Tags](https://realitybytes.blog/2017/06/02/detecting-and-tracking-ar-tags/)
* [ar_track_alvar - ROS Wiki](http://wiki.ros.org/ar_track_alvar)
* [Tracking AR Tags with ROS: Monocular Vision ](http://projectsfromtech.blogspot.com/2017/09/tracking-ar-tags-with-ros-monocular.html#google_vignette)