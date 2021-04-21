# KELO Tulip

This package contains the *KELO Tulip* software. This software takes a velocity vector for the overall platform and converts it to commands for the individual KELO Drives of the platform. 

## KELO Drives and how to build a platform
The [KELO Drives](https://www.kelo-robotics.com/technologies/#kelo-drives) is the patent pending novel drive concept for mobile robots developed by KELO robotics. These wheels can be attached to any rigid platform in order to transform it into a mobile robot. It can even be used to [robotize a container](https://www.kelo-robotics.com/customized-designs/#robotized-material-container). A few screws are enough.

After mechanically connecting the KELO Drives to your platform, you only need to connect them to power via an XT-60 power connector and to your computer via an EtherCAT cable. For multiple wheels, you should either use a Beckhoff Ethernet Switch or one of the KELO power distribution boards. After the wheels are powered and connected to your computer via EtherCAT, the software in this repository will help you make your robot move. 

You can move your mobile platform via a joypad for test purposes or use any software that publishes ROS `geometry_msgs/Twist` messages to the `cmd_vel` topic. See the Interface section below. The software can also be used without ROS. Please contact the developers for that.

## Permissions

Special permissions need to be granted to the executable, since it needs *RAW* access to the Ethernet port. Instead of starting it
as root the `setcap` tool can be used:
 
```
sudo setcap cap_net_raw+ep <name_of_executable>

```

Optionally, the command can be applied during the build process by passing the `-DUSE_SETCAP=ON` option to catkin. Default is `OFF`.

```
catkin_make -DUSE_SETCAP=ON
```
OR
```
catkin build kelo_tulip -DUSE_SETCAP=ON
```

**Note**: If you use this flag, you will be asked to input sudo password during
the build process. It would look like the build is going on but you need to keep
a lookout for `[sudo] password for <username>` in the output and enter the
password as soon as this prompt is visible. If not, the build process will
continue forever.

## Interface

Currently the software uses ROS as a middleware and is subscribed to the `cmd_vel` topic. This topic accepts [`geometry_msgs/Twist`](http://docs.ros.org/en/melodic/api/geometry_msgs/html/msg/Twist.html) messages. Any motion software that creates a velocity vector for the platform and publishes `geometry_msgs/Twist` messages to the `cmd_vel` topic can be used. The ROS package [`move_base`](http://wiki.ros.org/move_base) is an example that conforms to that. 

The platform can also be moved by a joypad. We recommend using the [`joy` ROS package](http://wiki.ros.org/joy).

## Parameters

The controller needs to know the location of the wheels in the body fixed frame of the platform as well as the offset of their pivot encoder (the encoder value when the wheel is oriented forward). Currently this information is encoded in `src/VelocityPlatformController.cpp` line 101 - 115 in the following format. 

```
wheel_params_[0].pivot_position.x = 0.175f;
wheel_params_[0].pivot_position.y = 0.1605f;
wheel_params_[0].pivot_offset = -2.5f;

```
Please adjust the array of the wheels with the correct number and location of the wheels on your platform. The next update will allow to put these values into a configuration file. 

