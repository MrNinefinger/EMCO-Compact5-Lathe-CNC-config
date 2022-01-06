# EMCO-Compact5-Lathe-CNC-config
LinuxCNC configuration for a EMCO Compact 5 converted to use steppers and a MESA 7i76E interface card.
This is my "working configuration", and it may not be optimal but its good enough for me (for now).

General Machine Configuration Notes:

The configuration includes a toolchanger that has 6 equaly spaced tool positions, being operated by a stepper motor and locking by reversing against a pawl.
The 'toolchanger.comp' is required for the toolchanger.  
The spindle parameters are for the stock motor but driven by a cheap AC to DC motor control, with the belt running on pulleys that are roughly 4:5 motor:spindle 
The lathe is configured with home switches at 'min' Z (tailstock end of lathe) and 'max' X (largest diameter position for cross slide)
Using the original 100 slot encoder disk,but with new optical sensors, 1 for pulse (A) and 1 for index.  I don't run in reverse so no need for a second pulse (B) to get quadrature and direction.
Spindle speed display is run through the lowpass filter to smooth it out.
Spindle at speed indication is set be true when within +/- 3.5% I believe if I am interpreting this correclty (in the .hal file - "  setp near.0.scale 1.035 ")

Installation Notes:

To install the toolchanger.comp file Linuxcnc-dev is neeeded, may have to add the repositories manually (llok up and add to synaptic package manager), as well as build essential

sudo apt-get update
sudo apt-get install build-essential
sudo apt-get install linuxcnc-uspace-dev

and the Synaptic package manager has to be closed before using the terminal to install stuff (i.e. toolchanger.comp)
open a terminal in the folder where the .comp file is and enter:

sudo halcompile --install toolchanger.comp

I had issues with sudo, can instead run in root and omit sudo prefix on commands:  su root
exit root when done!

Running it:

The tool table needs to have a 'tool' line configured for it to be able to select that tool (minimum is the tool # and pocket # entered in the tool table).

If the wrong tool # is lined up (i.e. the machine thinks it is on tool 4 but its really tool 6) issue a M61 T# Q# commmand for the tool # it should be. That will assign the correct tool number to the one that is in place.
Currently there is no command that I know of to jog back agaist the pawl, sending lots of individual tool change commands will eventually get it lined up enough that it ratchets when reversing against the pawl.  

no soft limit to prevent running into the chuck - did this to be able to machine a custom chuck back plate for my 80mm er32 chuck 

Things to work on:

1.  The spindle PID doesn't appear to be working,  when running at 50% speed and applying a load it doesn't try and speed up.  Need to investigate.
2.  for convenience add a reverse and forward toolchanger jog button (real or virtual)
3.  Investigat max velocity for Z axis, seems slow.  Play with micro stepping settings and step dir timming (changing the timing ont he mills 4th axis to 3000 improved the steppers performance significantly.)
