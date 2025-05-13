# SITL ArduPilot Demos

## SITL
SITL, or Software-In-The-Loop, is a simulation framework used primarily with the ArduPilot open-source autopilot system. It allows developers, testers, and operators to run the actual flight control firmware such as ArduCopter, ArduPlane, or ArduRover directly on a computer without the need for any physical flight hardware. SITL simulates all the onboard sensors, such as GPS, accelerometers, gyroscopes, magnetometers, and barometers, and responds to inputs just like a real drone would in flight. This enables users to test flight behavior, develop autonomous missions, debug scripts, or integrate ground control software (like MAVProxy, Mission Planner, or QGroundControl) in a safe and controlled environment. Since the software being tested is the same as what runs on the actual vehicle, SITL provides a highly realistic and reliable testing platform. It is especially valuable for validating new features, troubleshooting issues, or training users without the risk or cost of operating real hardware.

## Setting Up SITL Environment on Ubuntu WSL
To install and set up SITL simulation for ArduPilot, follow the steps below. This will allow you to run ArduPilot SITL in a virtual Ubuntu environment on your Windows machine.

### 1. Install WSL and Ubuntu on Windows 11
To begin using SITL on Windows 11, you will first need to install the Windows Subsystem for Linux (WSL) along with the Ubuntu distribution. WSL2 allows you to run a full Linux environment directly within Windows, which is essential for building and running ArduPilot's SITL. 
- Open PowerShell as Administrator and
- Enter the command
```sh
wsl --install
```
This will automatically install WSL2 and the latest version of Ubuntu. After your system reboots, Ubuntu will complete its installation, and you will be prompted to set a username and password. Once that is done, you can start using the Linux environment just like a native Ubuntu terminal.

### 2. Update Ubuntu and Install Git
After launching the Ubuntu terminal for the first time, it is important to update the package lists to ensure all software you install is up to date.
- Run the command
```sh
sudo apt-get update
```
- Then, install Git using
```sh
sudo apt-get install git
```
Git is required to download the ArduPilot source code from GitHub. This ensures you have access to the complete codebase, including the necessary simulation tools and modules.

### 3. Clone the ArduPilot Repository
With Git installed, the next step is to clone the official ArduPilot repository. This repository contains the source code for the autopilot software, as well as tools for running SITL. Use the following command to clone it along with all its submodules:
```sh
git clone --recurse-submodules https://github.com/ArduPilot/ardupilot.git
```
Then navigate into the repository with
```sh
cd ardupilot
```
The ```--recurse-submodules``` option is crucial because it ensures that all dependent libraries and modules are downloaded properly.

### 4. Install Required Dependencies
ArduPilot provides a script that installs all necessary dependencies for development and simulation. Run the script using:
```sh
Tools/environment_install/install-prereqs-ubuntu.sh -y
```
This script will automatically detect your system type and install packages like ```python3```, ```pip```, ```build-essential```, and other libraries required to compile the software. Once it's finished, you need to apply the updated environment variables by running:
```sh
. ~/.profile
```
This makes sure that tools and paths added by the installation script are immediately available in your terminal session.

### 5. Build the SITL Firmware
Before running SITL, you need to compile the vehicle firmware for software simulation. ArduPilot uses the ```waf``` build system. First, configure the build environment for SITL by running:
```sh
./waf configure --board sitl
```
Then, compile the firmware for your desired vehicle. For example, to build a simulated quadcopter, run:
```sh
./waf copter
```
You can also replace ```copter``` with ```plane```, ```rover```, or other supported vehicles. The build process may take several minutes depending on your system.

### 6. Run the SITL Simulation
Once the firmware is built, you can launch the SITL simulation using the built-in ```sim_vehicle.py tool```. A typical command to run a quadcopter simulation is:
```sh
./Tools/autotest/sim_vehicle.py -v ArduCopter -f quad --console --map
```
This command starts the simulated drone, opens a MAVProxy commandline interface, and launches a simple graphical map display. Thanks to Windows 11’s native support for Linux GUI applications in WSL2 will open directly on Windows desktop, just like regular applications.

## Setting Up FlightGear 3D Visualization in SITL
Developers working with ArduPilot's SITL simulation can optionally install and use FlightGear, an open-source flight simulator, to provide a real-time 3D visual representation of the simulated vehicle and its environment. This offers a far more immersive and realistic experience compared to the basic 2D maps and head up displays provided by tools like MAVProxy or Mission Planner.
When enabled, SITL sends aircraft state data such as position, orientation, and speed over UDP port 5503, which FlightGear listens to for rendering the 3D simulation. This setup allows to watch the virtual aircraft move through a realistic environment, which is especially useful for debugging behavior, validating flight paths, and showcasing autonomous missions.

### 1. Installing FlightGear on WSL Ubuntu
 ```sh
sudo apt-get update
```
```sh
sudo apt-get install flightgear
```
This will download and install the necessary binaries and basic scenery files. Additional scenery and aircraft models can be downloaded from within FlightGear itself or added manually later if needed.

### 2. Launching FlightGear for SITL
After installing FlightGear, you need to start it in a mode that listens for SITL simulation data. ArduPilot provides ready-to-use scripts located in the /ardupilot/Tools/autotest/ directory to make this easy. Depending on the type of vehicle you are simulating, open a new terminal window and run:

- **For quadcopters:**
```sh
./Tools/autotest/fg_quad_view.sh
```
- **For fixed-wing aircraft:**
```sh
./Tools/autotest/fg_plane_view.sh
```
These scripts configure FlightGear with the appropriate aircraft model, set the initial position, and bind it to the expected UDP port. After running the script, the FlightGear GUI should launch and display the aircraft at the default location.

### 3. Starting SITL with FlightGear Enabled
Once FlightGear is running, you can launch SITL and enable integration with the --enable-fgview option. It's highly recommended to start FlightGear before SITL to ensure a stable connection between the two processes. Here's an example command to start a simulated quadcopter at San Francisco International Airport (KSFO):
```sh
./Tools/autotest/sim_vehicle.py -v ArduCopter -L KSFO --console --map --enable-fgview
```
This will initialize the simulation at KSFO and start transmitting real-time vehicle state to FlightGear, which will visually track the drone’s position and attitude in a 3D environment. The location ```-L KSFO``` specifies the start point; other locations can also be used by modifying this parameter.

### 4. Remote Setup (Optional)
If you're running FlightGear on a separate machine from SITL for example, to offload rendering to a more powerful GPU system you can configure SITL to send data to a remote IP. This is done by passing the ```--fg-address``` option when starting SITL:
```sh
./Tools/autotest/sim_vehicle.py -v ArduPlane -L KSFO --enable-fgview --fg-address=<ip-address>
```
Replace <ip-address> with the IP address of the system running FlightGear. Make sure firewall and network settings allow UDP traffic on port 5503.
