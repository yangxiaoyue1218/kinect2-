Reference https://www.cnblogs.com/li-yao7758258/p/7445429.html

我用的是ubuntu14.04LTS,ROS 版本是indigo,kinect v2,我是用双系统装的ubuntu,关于怎么使用安装kinect2的安装以及使用的都在github上有着详细的说明

Kinect2 开源驱动：libfreenect2

kinect2--> ros的bridge：iai_kinect2
安装
安装libfreenect2，这个libfreenect2据说是它的驱动包，希望是稳定的，放在home目录下；
git clone https://github.com/OpenKinect/libfreenect2.git
安装好libfreenect2的依赖项

sudo apt-get install build-essential cmake pkg-config libturbojpeg libjpeg-turbo8-dev mesa-common-dev freeglut3-dev libxrandr-dev libxi-dev
安装libusb
sudo apt-add-repository ppa:floe/libusb
sudo apt-get update
sudo apt-get install libusb-1.0-0-dev
 
安装GLFW3
cd libfreenect2/depends
sh install_ubuntu.sh
sudo dpkg -i libglfw3*_3.0.4-1_*.deb
 
不要用sudo apt-get install安装glfw3,但可以用sudo apt-get remove libglfw3-dev卸载它。
所有以包形式安装的包都可以sudo apt-get remove来卸载它
接着编译库
cd ..
mkdir build && cd build
cmake ..
make
sudo make install

这时候插上kinect的数据线
运行lsusb,如果下面出现Bus 002 Device 003: ID 045e:02c4 Microsoft Corp.
Bus 002 Device 002: ID 045e:02d9 Microsoft Corp. 就说明连上了。
运行
  ./bin/Protonect
如果提示no device connected,是因为你的虚拟机默认设置是接usb2.0的口，而kinect v2要接usb3.0的口，所以要做出更改，但是用虚拟机装的ubuntu是连不上kinect的。

如果是要重装或是重新编译libfreenect2,则
cd
sudo rm -rf /usr/local/include/libfreenect2
cd /usr/local/lib
sudo rm -rf libfreenect2.so libfreenect2.so.0.2 libfreenect2.so.0.2.0
cd OpenNI2/Drivers
sudo rm -rf libfreenect2-openni2.so  libfreenect2-openni2.so.0
 
 
ROS接口安装
对于已经安装了Ros Indigo的Ubuntu14.04来说，使用下面的命令
cd ~/catkin_ws/src/
git clone https://github.com/code-iai/iai_kinect2.git
cd iai_kinect2
rosdep install -r --from-paths .

cd ~/catkin_ws
catkin_make -DCMAKE_BUILD_TYPE="Release"
rospack profile
 

rosdep install -r --from-paths .这一步可能会报错，但这个报错是正常的，不用管，不会影响最终结果。

接下来可以测试了。

新打开一个终端，运行
cd ~/catkin_ws/
catkin_make
source ./devel/setup.bash
cd src
roslaunch kinect2_bridge kinect2_bridge.launch
获取Kinect数据

再新打开一个终端，运行

rosrun kinect2_viewer kinect2_viewer

就可以正常显示图像了

