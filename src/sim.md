# Setting Up The Simulator

This guide will go through how to set up the simulator to simulate Autopilot
scripts or MavLink connections!

### System Requirements:
  
OS: Ubuntu 22.04+ (Or equivalent to it, WSL will also work, please note that
some functionality in WSL is limited)

## Create Working Environment for your simulator 
It is recommended to enclose your simulator in its own directory for ease of use.

## Installing QGroundControl  

[https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html#ubuntu](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html#ubuntu)

After installing QGC (QGroundControl): Go to application settings, and then
network settings. Add a connection via UDP and make the address 127.0.0.1:14551
and the connection port to be 14551

## Set up ArduCopter SITL

### Set up the build environment for ArduPilot
#### ubuntu
##### Install required packages:

```
sudo apt-get update
sudo apt-get install git
sudo apt-get install gitk git-gui
```

#### mac
##### install required packages
1. Install Xcode command line tools and homebrew (both likely already done)

    ```
    xcode-select --install
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
2. Install required packages

    ```
    brew update
    brew install genromfs
    brew install gcc-arm-none-eabi
    ```
3. Install the latest version of gawk
    
    ```
    brew install gawk
    ```
4. Install pip (again, likely already done)
    ```
    python -m ensurepip --upgrade
    ```

### Clone ArduPilot repository into the simulator directory

```
git clone --recurse-submodules git@github.com:ArduPilot/ardupilot.git
cd ardupilot
```

#### Install the required packages for SITL (Software In The Loop)

#### Ubuntu
```
Tools/environment_install/install-prereqs-ubuntu.sh -y
```

#### Mac
```
Tools/environment_install/install-prereqs-mac.sh
```

> NOTE: This installation can be quite lengthy, don't exit out just because its
> taking too long. Patience is a wonderful thing

Reload the path in order to make these changes take effect. Personally, I'd
recommend rebooting your system.

```
sudo reboot
```

After your system has finished rebooting:

### Build the code and configure it for ArduCopter

```
./waf configure
./waf copter
./waf clean
```

At this point, you can test if everything is working by running. You most likely will need to reload the path for this to work (open a new terminal window)

```
sim_vehicle.py -v Copter
mavproxy.py
```

If there are no errors, then everything is working. If there are issues, please
report them so we can investigate further.

## Getting the Scripts to run the simulator

For ease of use, I have created shell scripts that you can run to make things
easier

For those of you who are unfamiliar, a shell script is basically a script you
can run which will execute commands as if it were you typing things out in the
terminal (with a few exceptions)

You can clone the simulator-scripts repo into your simulator directory and the
scripts will be added into the directory

```
git clone git@github.com:uaarg/simulator-scripts.git
```

Next you need to make these shell scripts executable

```
chmod +x ./sitl.sh ./proxy.sh
```

## Installing Gazebo (3D Simulated Environment for Simulating Imaging Scripts in Flight)

Install Gazebo from binary, following the instructions from here:
https://gazebosim.org/docs/harmonic/install/

Follow these instructions for Gazebo Harmonic based on your OS:

https://github.com/ArduPilot/ardupilot_gazebo/blob/main/README.md


Test out if the Gazebo SITL works by following the Usage instructions from the ardupilot_gazebo README.md


## Running the Simulator

Open up 4 terminals

1. In the first terminal, run `./sitl.sh`
2. In the second terminal, run `./proxy.sh`
3. In the third terminal, run QGroundControl or Gazebo (depending on what you are testing)

The fourth terminal will serve as the place for you to run your python scripts.

To run a python script, navigate to the root of the directory with the script.
For example, navigate to the root of shepard and then you can run your scripts
as follows:

```
PYTHONPATH=. python3 <path to your script>
```

After the script is running, you will know if it is running if there is a print
statement in your console saying that the connection has been established

To start flying the drone in the simulator, you will need to go into the
mission planner, set the mode to `Guided` and then arm the drone and it will
start flying.
