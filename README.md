
### ROS Bridge to the Ensenso SDK (12/16/2016)

##### Drivers for retrieving point clouds from the ensenso camera

#### Author: [Olalekan Ogunmolu](http://twitter.com/patmeansnoble)

#### [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
- [Dependencies](#dependencies)
  - [ROS](#ros)
  - [uEye Driver](#ueye-driver)
  - [EnsensoSDK](#ensensosdk)
  - [Point Clouds Library](#point-clouds-library)
  - [Optional Dependencies](#optional-dependencies)

- [Network Configuration](#network-configuration)
- [Using Ensenso on Linux](#using-ensenso-on-linux)
- [Training Data for Mannequine Head](#training-data-for-manikin)
- [Head Segmentation](#head-seg)
- [FAQs](#faqs)
- [Citation](#citation)

#### Introduction
The sensor captures a single 3D profile for each camera exposure. The Ensenso camera uses the projected texture stereo vision technique and is fitted with two global shutter CMOS sensors and a pattern projector, which projects a random dot pattern onto the object to be captured.

<div class="fig figcenter fighighlight">
  <img src="/ensenso/images/0001_gray.png" height="250" width="45%" align="left" style="border-left: 1px solid black;">
  <img src="/ensenso/images/fancy2.png" height="250" width="45%" align="right" style="border-left: 1px solid black;">
  </br>
  <div class="figcaption" align="middle">
  </div>
</div>
<br>
  Figure 1: Left: Raw Image from the Sensor's Camera<br>
  Right(left: inset): Point Cloud from scene of the head &nbsp; <br>
    Right(right inset): Point Cloud after segmenting face from head

#### Dependencies

For 3D visualization, we need OpenGL 3.0 compatible graphics card and drivers. A minimum iof 2GB RAM and 2GHz CPU frequency is required to run the camera. This code is C++-11 compatible. It would not compile without enabling the c++ 11 options on your compiler. A minimum of g++ 4.8 or VS 2012 is required to run this code.

##### ROS

 ROS Indigo/Jade/Kinetic. Possible backwards compatibility with ROS Hydro but not tested.

##### uEye Driver

  Please make sure you have the IDS uEye driver installed in version 4.00 or higher. For N20, N30 and N35 cameras a driver with version 4.41 or higher is required. Please visit [ensenso download](https://www.ensenso.com/download) page and download and install the latest driver for your system.

##### EnsensoSDK

  After the uEye driver installation is complete, download the latest EnsensoSDK software release from [ensenso download](https://www.ensenso.com/download) and follow the instructions on screen to install the software.

#####   Point Clouds Library

  The sensor requires pcl-1.8.0.  Clone the 1.8.0 trunk from the [pcl github repo](https://github.com/PointCloudLibrary/pcl/tree/pcl-1.8.0), then follow the readme instructions to install. Remember to turn on `ensenso` features during compilation e.g. `cmake -D WITH_ENSENO=ON ../` from your build directory.

  A good place to install might be in your Documents directory. After installing, be sure to set the directory of the cmake pcl config file to that where the libraries are installed in the `CMakeLists.txt`. For example, the config file could be at `/usr/local/share/pcl-1.8/PCLConfig.cmake`. Go to the CMakeLists.txt file within the root directory of this project and amend the PCL directory/path to the one where your installer saves it (you can easily find this in the `installer_manifest.txt` file of your build directory).

##### Optional Dependencies

  *	 VTK LIBRARY (Optional)
  *	 Boost Library (Optional)

#### Network Configuration

Open `idscameramanager` with root privileges and manually set the ip address of ensenso to a persistent one. A typical one might be `192.168.1.11` for example.

In our setup, we connect the camera to a local router and connect our system to the same router so that the desktop can access the internet while having access to the ensenso camera.

In addition, ensure your ubuntu firewall is turned off:

```bash
 sudo ufw disable
```

If you encounter problems during set-up, it might be worth the while runing the `ueyesetupid` executable packed into the gunzipped tarball available at [setup id](http://ecs.utdallas.edu/~opo140030/sensors/uEye-Linux-4.81-64-bit.tgz). You would want to unzip the tar ball and run the `ueyesdk-setup-4.81-eth-amd64.gz.run` file. This should install configuration files to your `/usr/local/share/ueye/bin` directory from where you can configure the id of your sensor (which should be 1 by default).

#### Using Ensenso on Linux

1.	Power up the sensor. Connect the Power/LAN cable to the sensor and the other end to the Ethernet port on the RJ45 connector port on your computer

2. Clone this repo to your catkin src folder then `catkin build` from your catkin workspace root.

3. Run the program

- The bridge is our ros interface to the sensor.

  We can bring up the ros nodes with,

  ```bash
  Terminal 1$ rosrun ensenso ensenso_bridge
  ```

- To visualize the ir and point clouds of the environment, we would run

  ```bash
  Terminal 2$ rosrun ensenso_viewer ensenso_viewer
  ```

- Segmentation described in the paper is performed in

  ```bash
    rosrun ensenso_seg ensenso_seg
  ```

- Recognition and Detection with CMU Open Face Detector

  ```bash
    rosrun ensenso_detect ensenso_detect
  ```

#### FAQs

##### I am having issues connecting to the sensor even though my code compiles

Be sure the id of the sensor is properly set using the `ids camera manager` configuration utility that ships with [ueye](http://ecs.utdallas.edu/~opo140030/sensors/uEye-Linux-4.81-64-bit.tgz). You can find this in the images folder of this package.

##### I am having issues setting up a dual GigEthernet and wireless connection

Please follow the advice listed in the Installation section

##### `em` and `empy` colliding in namespace

I solved this problem by simply uninstalling em, and installing empy:

pip uninstall em
pip install empy

See this [issue ticket](https://github.com/ros/genmsg/issues/63).

##### Can't get to resolve the python vesion in the bash namespace

Catkin build as follows:

`cb  -DPYTHON_VERSION=2.7`. Be sure to delete your devel, build and logs spaces before re cb'ing

##### Running into package dependencies cyclical errors

remove the ensenso_seg files from your catkin built devel space

`rm devel/liv/.private/ensenso_seg -rf`

#### Other queries

If you run into further issues, feel free to open an issues ticket.

#### Citation

If you used `ensenso` in your work, I would be glad if you could cite it.

```tex
@electronic{ensenso,
  author = {Ogunmolu, Olalekan P.},
  title = {{ensenso pointclouds in C++}},
  year = {2016},
  url =  {\url{https://github.com/lakehanne/ensenso}},
  note = {Accessed December 24, 2016}
}
```
