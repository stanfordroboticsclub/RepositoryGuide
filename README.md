# RepositoryGuide
The goal of this repository is to maintain a list of modules that are in a state that is good for reuse, and to describe how to use them


## Raspberry Pi Setup
- [RPISetup]() - Automates setting up raspberry pis in a repeatable way. Also sets up a read only file system (highly recomended to make your robots much more reliable) and the subnet (10.0.0.X) other modules expect

- [UDPComms](https://github.com/stanfordroboticsclub/UDPComms) - Our simple alternative to ROS. Sends messages between processes on differnet computers on the 10.0.0.X subnet

- [CameraStream](https://github.com/stanfordroboticsclub/RoverCam) - Nice wrapper around gstreamer to stream video from usb, and rpi cameras with minimal latency. Can also stream images from opencv


## Network Setup

- [RemoteVPN](https://github.com/stanfordroboticsclub/RemoteVPN) - Allows you to connect to your robot from anywhere in the world

- [uDHCPd](https://github.com/stanfordroboticsclub/uDHCPd) - Sets up a DHCP server that assigns IPs on the correct subnet to devices that don't set their own static IP


## Interfacing with Hardware
- [PS4Joystick](https://github.com/stanfordroboticsclub/PS4Joystick) - Talks to a PS4 controller over bluetooth

- [UDPLidar](https://github.com/stanfordroboticsclub/LidarUDP) - Talks to an rplidar laser scanner

- [RoverGPS](https://github.com/stanfordroboticsclub/RoverGPS) - Talks to the sparkfun RTK GPS modules. Includes code for creating our own RTCM basestations

- [RoverIMU](https://github.com/stanfordroboticsclub/RoverIMU) - Fuses a magnetometer reading from the HMC6343 and the LSM9DS1's gyro for a non shit estimate of heading

- [RoverODrive](https://github.com/stanfordroboticsclub/RoverODrive) - Talks to the ODrive brushless motor controller

- [RoverArm](https://github.com/stanfordroboticsclub/RoverArm) - Contains a bunch of extra code but contains a nice library to talk to the roboclaw motor controller



Module Specification
-------------
Having a bunch of premade modules isn't much use if there isn't a consistant way to use them. This section tries to outline some specification modules should try to conform to. Hopefully this easily installed/enabled in a predictable way, even by someone unfamilar with the module in question.

Many of those modules will interact with each other over the rover network (with IP's in range 10.0.0.X) using the [UDPComms Library](https://github.com/stanfordroboticsclub/UDPComms). 

Specifications:

- Keep each seperate and potencially reusable modlues in its own git repo which can be downloaded with a `git clone (address)`

- Each module shall contain a `install.sh` script which will prepare the module to be used **including downloading and installing any requirements** and symlinking the service files to the correct places. In practice this helps immensely when you try to duplicate the repository on another pi and need to hunt down dependancies again.

- Each module shall be documeted using a `README.md` (duh) and for modules that use UDPComms the topics is publishes and subscribes to should be listed on the [CS Comms System](https://docs.google.com/spreadsheets/d/1pqduUwYa1_sWiObJDrvCCz4Al3pl588ytE4u-Dwa6Pw/edit?usp=sharing) document

- Depending on what the module is writen in there are different ways to launch programs. To make this consistant we try to manage everything through systemd service files. This also allows for programs to start on boot and for crashed programs to automatically restart. This is super nice as in many cases this negates the need to ssh during normal opperation to set things up. Read the [intro to systemd](https://www.devdungeon.com/content/creating-systemd-service-files) and learn more about writing [service files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files#anatomy-of-a-unit-file). This allows programs to be manipulated as a systemd service using the following commands in the table.


In an ideal world those programs running on remote pis could also be controlled using the `rover` command that is part of [UDPComms](https://github.com/stanfordroboticsclub/UDPComms). Currently we don't have public keys set up on the Pis and automatic password typing isn't as reliable as I'd want it to be. The commands are conveniant when they work and someday we can improve the reliabliity of `rover`. If the service name matches the host name (which makes sense if one pi is only running one service such as `gps.service` running on the `gps.local` pi) one can be ommited.

| Command | Descripion | Rover (not super reliable) |
|---------|------------|-------|
| `sudo systemctl status name` | tell us what the service is doing right now | `rover status host service` |
|`sudo systemctl start name` | start the service right now | `rover status start service` |
|`sudo systemctl stop name` | stop the service right now | `rover status stop service` |
|`sudo systemctl disable name` | stop the service from starting on boot | `rover disable host service` |
|`sudo systemctl enable name` | make the service start on boot | `rover enable host service` |
|`journalctl -u name` | display the output of the service | `rover log host service` |


