# Autopilot System

This page gives a high-level overview of how UAARG's autopilot system works and
where its code lives. Only basic Python knowledge is assumed.

## Overview

A Python script running on an onboard single-board computer (SBC), such as a
Raspberry Pi, sends commands to the autopilot board (a Pixhawk). The Pixhawk
executes those commands by signalling the ESCs (Electronic Speed Controllers),
which in turn drive the motors.

Our work focuses on the Python side of this system. The Pixhawk firmware, ESCs
and motors are third-party components and are not covered here.

## Software Layers

From lowest to highest level:

| Layer | Description |
| --- | --- |
| **MAVLink** | The messaging protocol used to communicate with the Pixhawk. A MAVLink connection runs over TCP or UDP and behaves much like a standard network connection. |
| **PyMAVLink** | A Python library for opening a MAVLink connection and sending and receiving messages. |
| **mavctl-python** | Our in-house library built on PyMAVLink. It manages connections and hides PyMAVLink's low-level details. It replaces the deprecated DroneKit library. |
| **Flight scripts** | Scripts that call `mavctl-python` to define flight parameters (position, altitude, velocity, etc.) and maneuvers. |

Most autopilot work involves either developing `mavctl-python` itself or
writing flight scripts with it. You should rarely need to use PyMAVLink
directly, and DroneKit should not be used for new work.

For a list of available MAVLink messages, see the [MAVLink common message
set](https://mavlink.io/en/messages/common.html).

## Repositories

- **[Shepard](https://github.com/uaarg/shepard)** – the main repository for
  flight-related code.
  - [`src/flight_tests`](https://github.com/uaarg/shepard/tree/main/src/flight_tests)
    – existing flight scripts, which are good examples to learn from.
  - [`src/modules/autopilot`](https://github.com/uaarg/shepard/tree/main/src/modules/autopilot)
    – autopilot modules. `navigator.py` contains most of the navigation
    methods used by flight scripts.
- **[mavctl-python](https://github.com/uaarg/mavctl-python)** – the autopilot
  control library. Development is ongoing; open tasks are tracked on the
  [project board](https://github.com/orgs/uaarg/projects/7). New members should
  start with issues labelled "Good First Issue".

## Getting Started

1. Make sure you are familiar with Python, Git and GitHub (see [Required
   Tools](./tools.md)).
2. Clone the repository you want to work on.
3. Test your code with the [Autopilot Simulator](./sim.md).

A good first exercise is writing a simple flight script of your own and running
it in the simulator.

If you have any questions, feel free to reach out to the Software team.
