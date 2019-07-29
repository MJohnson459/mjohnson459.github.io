---
layout: post
title:  "Robotic Operating System (ROS)"
date:   2019-02-03 15:42:05+00:00
categories: robotics
---

## What is ROS?

ROS is a large collection of microservices communicating over custom publisher-subscriber messaging
system. A shared IDL allows the services to be reused easily when creating a new robotics project.
It is used by almost all universities teaching robotics and often recommended as THE thing to learn
to get into robotics (and who doesn't want that?!)

It focuses heavily on this quick-to-get-started approach and can probably attribute much of it's
success to the tactic. From the beginning there are step-by-step guides to installing, developing,
and running new `stacks` making it great for students and hobbiests alike.

- Been around for ... years developed in colleges
- A collection of libraries for robotics
- A common communication protocol and language
- Microservices
- A key/value store
- A custom build runner (cmake wrapper)

### Requirements

- Easy to learn and get started (target students)
- Supports multiple languages (students use python, industry C/C++)
- Easy to share and reuse drivers and packages

## Ecosystem

With over 5140 packages (1833 repositories) (TODO are these unique?
[[https://index.ros.org/stats/]]) it is easy to see it has done //something// right to get people
contributing.  This number includes numerous sensor drivers, from a simple temperature sensor to the
Kinect 3D camera.

ROS has a collection of common messages that are used widely within the ecosystem
[[https://wiki.ros.org/common_msgs]]. These messages form the basis of all ROS packages and are what
allow thousands of packages to be interoperable with each other.

### Problems

Finding packages remains one of the pain points of ROS with only a simple wiki available which is
more often than not outdated. Some of this is remedied by a new effort at https://index.ros.org/
although it is still in an immature state.


## Messages

ROS uses a custom message format to define messages sent between different processes. In general
these look like a simplified C struct with some python comments:

```name=sensor_msgs/PointField.msg
# This message holds the description of one point entry in the
# PointCloud2 message format.
uint8 INT8    = 1
uint8 UINT8   = 2
...

string name      # Name of field
uint32 offset    # Offset from start of point struct
uint8  datatype  # Datatype enumeration, see above
uint32 count     # How many elements in the field
```

Each message is flat but may include other messages as fields:

```sensor_msgs/PointCloud.msg
# Time of sensor data acquisition, coordinate frame ID.
Header header

# Array of 3d points. Each Point32 should be interpreted as a 3d point
# in the frame given in the header.
geometry_msgs/Point32[] points
...
```

To use these messages there exist language generators which take the message files and output a
language specific interface (i.e. in C++ this would be a header file). The new file contains a few
helper functions and the encoding/decoding functions. Importantly it also includes a checksum so
that any changes to the original message invalidate the header.

### Problems

When the message format was created 12 years ago (2007) it was good enough but has since become
antiquated compared to modern formats. It isn't as compact as protobuf or CBOR nor as easy to use as
JSON or YAML. It doesn't support optional fields and the checksum means any changes to the message
make it backwards incompatible. In reality this means all packages must be either meticulously
managed or build together from source.


## Build system (catkin, ament, colcon)

ROS is build with CMake...kind of. It uses a maddening series of "tools" and configuration files to
attempt to hide the complexities of CMake from the novice user. Unfortunately this mostly serves to
invalidate most of the useful things CMake can do and means packages developed for ROS can't use any
of the new C++ package managers easily.

Not only that, the core ROS developers seem to enjoy changing the name of the build system every
year or so just to stop you being able to script anything. In the past 5 years there have been 5
build systems:
- rosbuild
- catkin
- catkin-tools
- ament
- colcon

Like wtf?

## Process Management (roslaunch, rosrun, etc)

## Blackboard (rosparam)

## RPC (services, actions)

## Publisher/Subscriber (topics)

## Package management (?)


## General
- a core library that isn't semantically versioned
- core team is rewritting the system as a second iteration (ROS2)
  - Little effort to improve original problems
  - Massive scope creep
