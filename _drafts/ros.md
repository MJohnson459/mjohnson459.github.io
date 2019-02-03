---
layout: post
title:  "Robotic Operating System (ROS)"
date:   2019-02-03 15:42:05+00:00
categories: robotics
---

## What is ROS?

... A project from a lost era ...

There was a time when a lone developer (or two) could crack open a text editor and bash out an
operating system with no outside help. Time was of no consequence, the giants hadn't reach
puberty, and an entire computer system could be understood by a smart kid.

Originally created in 2007 ROS feels like one of those projects. The developers created from nothing
a huge array of tools and gadgets to help make developing a robot as easy as possible. Programming a
robot became commonplace at universities and the hobbyist scene embraced it. Packages were created
for every sensor imaginable, algorithms were wrapped in ROS and the community rejoiced at how
quickly they could get their own R2D2 running around beeping censored obscenities.

But all was not right in the world.

Over time the core libraries grew, pains developed, extra mechanical limbs sprouted. Other
libraries were created to solve the individual problems by teams bigger and more experienced. They
included documention, they were easy to build, they were constantly being improved. And slowly
ROS started becoming obselete in all of the areas it once excelled at. It's message passing was not
as fast as protobuf or as user friendly as JSON. It's centralised publisher-subscriber model acted
as a single point of failure and made multi-vehicle scaling impossible. Its once superior build
system was overtaken by CMake, the tool once so hideously unfriendly it needing wrapping to be even
considered. Finally to top it all of, a dozen new languages rose to the front stage of the
software world bringing with them an array of tools and features expected by new developers.

And so the core team did what hundreds of engineers do every day and decided to start again. This
time it would be different. A new project was created called ROS2 (and who says creativity is
dead?!).

## ROS2

And so the small team looked about and took in the experience the software world had now gained.
They wrote design documents, followed processes, and painted their vision to the community. There
were two main changes the new system set out to achieve, the single point of failure with the
publisher-subscriber system, and the lack of reusable core between different language clients. To
solve the first, DDS was chosen as the new messaging system. This would allow the team to reduce
their maintenance burden by using commonly available libraries. The solve the second, they decided
to rewrite the core functionality in pure C library that could be called from any other language.

The team crouched down at the starting line, the crowd cheered, the gun fired, and the team took off
at the speed of a legless hamster.

Three years passed with only minor updates until out of the blue it was announced ROS2 was ready for
release. They had done it! DDS now formed the core of the messaging system. There was a pure C
library implementation and a C++ and Python client. There was a new build system called Ament. There
was a sketchy clown in the background. It had it all!

...or so it seemed for about 20 minutes.

On inspection, the new build system looked suprisingly similar to the old. The new C library seemed
awfully small for all of the promised features. The DDS wrapper was so large it would be less
maintanable than before. The clown was smoking a crack pipe.

None of the promised new features materialised and since they had started the original system had
only aged more. Boils had begun to fester. Small paper cuts had grown into machette wielding madmen.
The system continues to be released every 6 months but no new features are present.

Since then ROS2 has continued to improve and it expects to have gained feature parity with the
original sometime this year. Tutorials are still sparse and 90% of the problems of the original
still exist. The project still very much feels like it was written in someones garage...C'est la vie

Well, at least Sarah Connor can rest easy...

---

other androids in the class started getting faster, leaner, more friendly but little ROS was just
getting bigger. The developers lovingly cared for it, covering its flaws, replacing its batteries,
measuring is oil levels, but in the end there was nothing they could do. Not-so-little ROS was
chained to a college lamp post and left to hand out students homework.

And the developers started again. They reviewed their creation with care, noted why it was
different, understood how it grew, and set about creating a new foundation. They saw it's five arms
and two heads and agreed it needed a stronger oil pump. They saw how its awkward body shape make it
impossible to wear standard clothes and so set about creating a woolen mill. They saw how it
wasn't as friendly as others in it's class and so they avoided giving their new creation a mouth.



---

They started again, now aware

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

## Ecosystem

With over 5140 packages (1833 repositories) (TODO are these unique?
[[https://index.ros.org/stats/]]) it is easy to see it has done //something// right to get people
contributing.  This number includes numerous sensor drivers, from a simple temperature sensor to the
Kinect 3D camera.

ROS has a collection of common messages that are used widely within the ecosystem
[[https://wiki.ros.org/common_msgs]]. These messages form the basis of all ROS packages and are what
allow thousands of packages to be interoperable with each other.

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


## Catkin/Ament/Colcon/CMake
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

## General
- a core library that isn't semantically versioned
- core team is rewritting the system as a second iteration (ROS2)
  - Little effort to improve original problems
  - Massive scope creep
