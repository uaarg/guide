# Intro to the Autopilot System 

This guide will go through the crucial information needed to understand how the Autopilot system works on the drone.
I will be following a step-by-step approach, explaining every step that is taken in the operation of the autopilot system at a high level.

This guide will only require a limited knowledge of programming (Python to be specific) to fully understand as a lot of these concepts are fairly high level.

This guide is intended for anyone who is interested in learning about how the UAARG Autopilot system works, whether you are a seasoned veteran in the club or if you are just joining along for the ride.

My hope is that by the end of this guide you gain a surface level understanding of how the autopilot system works.
If at any point you need clarification, feel free to ask around.

## Basic Abstraction 

The TLDR of the Autopilot system is that there is a Python script which runs on a small SBC (Single-board computer) such as a Raspberry Pi that sends messages to our Autopilot board (Pixhawk).
From there, the Pixhawk will handle the input received from the Python script and will execute the command it receives by sending output to the ESC (Electronic Speed Controller) which translates the Pixhawk's signal into a rotation speed which is then sent to the motors.

The Autopilot system is in place in the first part of the abstraction, that is to say it is mainly dealing with the "Python Scripting" of the system. The rest of the system is either third party or lies in "if it ain't broke don't fix it" territory and as such will not be discussed in depth in this guide.


### What does "Python Scripting" mean?

The term I used previously, "Python Scripting", is a blanket term for our entire autonomous flight scripting ecosystem and has many layers to it which will be covered in this guide. 

The lowest level of this abstraction is MAVLink. `MAVLink` is a messaging protocol which interacts between a higher level in the abstraction and the Autopilot board. Essentially, MAVLink has a bunch of commands which are encoded in a format that the Pixhawk can read.
To put it simply, MAVLink is the thing that bridges the gap between Python and the Autopilot board, allowing for lightning fast, reliable communications between the scripting infrastructure and the autopilot board. 

The way that `MAVLink` is interacted with from Python is in a library known as `PyMAVLink`. `PyMAVLink` effectively provides a structure to allow `MAVLink` messages to be sent and received in a Pythonic manner. 
The way `PyMAVLink` works is that there is a `MAVLink` connection established at the start, and then commands are formatted (with the desired paramters) and send or received via the `MAVLink` connection. 

> The `MAVLink` connection is actually just a TCP or UDP connection and behaves very similarly to standard network connections

One layer of abstraction above `PyMAVLink` is where our new, in development library steps in. This library, `mavctl-python` is a replacement to the depreciated `Dronekit` and allows for the management of `MAVLink` connections via `PyMAVLink` and also handles all of the lower level verbose of `PyMAVLink`.
Historically, we have been using `Dronekit` for a long time but recently there has been an attmept phase `Dronekikt` for a number of reasons, primarily being that it is depreciated.
What `mavctl-python` allows the ability to develop flight scripts in a very easy and intuitive manner. 

The highest layer of abstraction is the flight script. The flight script is where the `mavctl-python` functions are called. At this level of abstraction, the autopilot system is very easy and intuitive to follow, and as such, I would highly recommend a beginner to try write their own flight script just to get a feel for how it works.
The flight script is also where specific paramters (position, altitude, velocity, etc.) are defined and specific maneuvers are defined.

Working on the Autopilot system, you will primarly be working either on `mavctl-python` or you will be developing new maneuvers or control protocols with `mavctl-python`.
There shouldn't really be a time where you will have to use `PyMAVLink` directly and I would strongly (emphasis on strongly) advise against using `Dronekit` if `mavctl-python` is said to be working and reliable during the time that you are developing.



## Where the hell is everything?

As someone who is newer to the Autopilot system, a common issue one faces is confusion regarding where all of the cool autopilot things are. 

### Where is the main repo where all of the flight test specific scripts and tools are located?

`Shepard` is the main repository where our flight related scripts are kept. (https://github.com/uaarg/shepard)

Our flight test scripts are kept here (https://github.com/uaarg/shepard/tree/main/src/flight_tests)
Feel free to browse through some of these just to get a feel for how the existing flight scripts work.

The Autopilot files and classes can be found here (https://github.com/uaarg/shepard/tree/main/src/modules/autopilot)
This folder contains most of everything that is regarding Autopilot. The main file of interest is navigator.py as that one contains a lot of the navigation methods that are used in flight scripts.


### Where the hell is mavctl-python?

`mavctl-python` is located on the UAARG Github page along with Shepard. (https://github.com/uaarg/mavctl-python)
In `mavctl-python` you can find a lot of the various navigation and otherwise that goes on with it so feel free to look through if you are trying to familiarize yourself with `mavctl-python`

> NOTE: Development of `mavctl-python` is ongoing. If you are looking to contribute, check out our project here (https://github.com/orgs/uaarg/projects/7) and feel free to pick up an issue.
> It is recommended that newer members start out with issues that are marked as "Good First Issue" 


## How do I get started?

Before you can get started working on the Autopilot system (from a programming perspective), there are a few things you must be familiar with.

- Python
- Git
- Github 


> See the following for more info on the required tools you should be familiar with before getting started.
> https://uaarg.com/guide/tools.html

From here, you can clone the repository you would like to work on to your local system and start coding away. 

As for simulating your code on the Autopilot simulator, please consult the "Autopilot Simulator" Guide. 


If you have any questions, don't hesitate to reach out. 

have fun gang :fire:
