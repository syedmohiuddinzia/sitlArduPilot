# SITL ArduPilot Demos

## SITL
SITL, or Software-In-The-Loop, is a simulation framework used primarily with the ArduPilot open-source autopilot system. It allows developers, testers, and operators to run the actual flight control firmware—such as ArduCopter, ArduPlane, or ArduRover—directly on a computer without the need for any physical flight hardware. SITL simulates all the onboard sensors, such as GPS, accelerometers, gyroscopes, magnetometers, and barometers, and responds to inputs just like a real drone would in flight. This enables users to test flight behavior, develop autonomous missions, debug scripts, or integrate ground control software (like MAVProxy, Mission Planner, or QGroundControl) in a safe and controlled environment. Since the software being tested is the same as what runs on the actual vehicle, SITL provides a highly realistic and reliable testing platform. It is especially valuable for validating new features, troubleshooting issues, or training users without the risk or cost of operating real hardware.

## Installation
To install and set up SITL simulation for ArduPilot, follow the steps below. This will allow you to run ArduPilot SITL in a virtual Ubuntu environment on your Windows machine.

### 1. Install WSL and Ubuntu on Windows 11
To begin using SITL on Windows 11, you will first need to install the Windows Subsystem for Linux (WSL) along with the Ubuntu distribution. WSL2 allows you to run a full Linux environment directly within Windows, which is essential for building and running ArduPilot's SITL. 
- Open PowerShell as Administrator and
- Enter the command ```wsl --install```.
This will automatically install WSL2 and the latest version of Ubuntu. After your system reboots, Ubuntu will complete its installation, and you will be prompted to set a username and password. Once that is done, you can start using the Linux environment just like a native Ubuntu terminal.

### 2. Update Ubuntu and Install Git
After launching the Ubuntu terminal for the first time, it is important to update the package lists to ensure all software you install is up to date.
- Run the command ```sudo apt-get update```.
- Then, install Git using ```sudo apt-get install git```.
Git is required to download the ArduPilot source code from GitHub. This ensures you have access to the complete codebase, including the necessary simulation tools and modules.

### 3. Clone the ArduPilot Repository
With Git installed, the next step is to clone the official ArduPilot repository. This repository contains the source code for the autopilot software, as well as tools for running SITL. Use the following command to clone it along with all its submodules:
```sh
git clone --recurse-submodules https://github.com/ArduPilot/ardupilot.git
```
