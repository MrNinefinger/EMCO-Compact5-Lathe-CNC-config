# EMCO-Compact5-Lathe-CNC-config
LinuxCNC configuration for a EMCO Compact 5 converted to use steppers and a MESA 7i76E interface card.
This is my "working configuration", and it may not be optimal but its good enough for me (for now).

General Machine Configuration Notes:

The configuration includes a toolchanger that has 6 equaly spaced tool positions, being operated by a stepper motor and locking by reversing against a pawl.
- even positions are for rectangular tools held perpendicular to the spindle axis (i.e. standard turning / profiling bits)
- odd positions are for round shank (10.0mm) tools held parallel to the spindle axis. (i.e drills, boring bars, etc)

The 'toolchanger.comp' is required for the toolchanger.  
The spindle parameters are for the stock motor but driven by a cheap AC to DC motor control, with the belt running on the pulleys that are roughly 4:5 motor:spindle (i.e. highest speed setting).

The lathe is configured with home switches at 'min' Z (tailstock end of lathe) and 'max' X (largest diameter position for cross slide).
Using the original 100 slot encoder disk, but with new optical sensors, 1 for pulse (A) and 1 for index. I don't run in reverse so no need for a second pulse (B) to get quadrature and direction.
Spindle speed display is run through the lowpass filter to smooth it out.
Spindle at speed indication is set be true when within +/- 3.5% I believe if I am interpreting this correctly (in the .hal file - "  setp near.0.scale 1.035 ")

Installation Notes:
Install Debian latest version from Debian.org
- NOTE 1 - ensure that the target computer has a working wifi or wired connection for Internet - will cause lots of issues if install proceeds without internet.
- NOTE 2 - when installing Debian, use a blank for the ROOT password, if a ROOT password is entered then SUDO will not work and will be forced to use ROOT to do all admin tasks.
- NOTE 3 - select Xfce as the desktop (familiarity reasons)

Install the realtime kernel  (PREEMPT-RT) that LinuxCNC needs:
- sudo apt-get install linux-image-rt-amd64
- reboot and ensure the realtime kernel is selected (has -rt- in it)
- can check the version using a terminal and typing uname -v
- add the LinuxCNC Archive signing key to opt keyring:
-Download:  https://www.linuxcnc.org/linuxcnc-install.sh and save to a folder
open folder and open terminal there
run chmod +x linuxcnc-install.sh (makes it executable)
then run the installer:  Sudo ./linuxcnc-install.sh  (installs LinuxCNC plus a whole lot of dependancies)
also install the uspace-dev (not the suggestion in the script has an error and is missing uspace (31/12/2025)
- sudo apt-get install linuxcnc-uspace-dev
- sudo apt-get install build-essential (needed to compile the toolchanger.comp later)
install GIT command line interface (CIL)
- sudo apt-get install git
- git --version should show it is installed by providing a version #
- set your email address:
- git config --global user.email "your.email@example.com"  (include the quotes)
- set your username:
- git config --global user.name "Your Name"
  
- Run LinuxCNC from the applications menu and open the Axis sim (or any one) and let it create the folders
(makes the folder  /home/PC-name/linuxcnc and the sub folders /config and nc_files

Clone this respository to the configs folder in Linuxcnc
~/linuxcnc/configs$ git clone https://github.com/MrNinefinger/EMCO-Compact5-Lathe-CNC-config

To install the toolchanger.comp 
open a terminal in the folder where the .comp file is (in the /linuxcnc/configs folder that was cloned above) and enter:

- sudo halcompile --install toolchanger.comp

If having issues with sudo, can instead run in root and omit sudo prefix on commands:  su root
exit root when done!

Running it:

The tool table needs to have a 'tool' line configured for it to be able to select that tool (minimum is the tool # and pocket # entered in the tool table).

If the wrong tool # is lined up (i.e. the machine thinks it is on tool 4 but its really tool 6) issue a M61 T# Q# commmand for the tool # it should be. That will assign the correct tool number to the one that is in place.
Currently there is no command that I know of to jog back agaist the pawl, sending lots of individual tool change commands will eventually get it lined up enough that it ratchets when reversing against the pawl.  

no soft limit to prevent running into the chuck - did this to be able to machine a custom chuck back plate for my 80mm er32 chuck 

Things to work on:

- [ ]  The spindle PID doesn't appear to be working,  when running at 50% speed and applying a load it doesn't try and speed up.  Need to investigate.
- [ ]  for convenience add a reverse and forward toolchanger jog button (real or virtual)
- [ ]  Investigate max velocity for Z axis, seems slow.  Play with micro stepping settings and step dir timming (changing the timing ont he mills 4th axis to 3000 improved the steppers performance significantly.)
- [ ]  figure out gearing in linuxcnc to be able to select the Low gear (pulley) settings and have the commanded speed be correct, as well as have High gear (pulley) commanded speeds be correct.
- [ ]  add back real buttons and an encoder for jogging, MDI entry, etc.
- [ ]  investigate QTdragon screens (needs Linuxcnc 2.9  which will probably break everything!)
- [ ]  add a seperate set of files for Linuxcnc installation tips and hints (i.e. change wicd to network manager, setup 2nd ethernet connection for MESA and wireless for internet, etc.)
- [ ]  figure out how to change the default loaded .ngc file to something not dangerous if somehow you accidentally hit cycle start (more importatnt on the mill)

