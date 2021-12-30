# EMCO-Compact5-Lathe-CNC-config
LinuxCNC configuration for a EMCO Compact 5 converted to use steppers and a MESA 7i76E interface card.

The configuration includes a toolchanger that has 6 equaly spaced tool positions, being operated by a stepper motor and locking by reversing against a pawl.
The 'toolchanger.comp' is required for the toolchanger.

The tool table needs to have a 'tool' line configured for it to be able to select that tool (minimum is the tool # and pocket #).

If the wrong tool # is lined up (i.e. the machine thinks it is on tool 4 but its really tool 6) issue a M61 T# Q# commmand for the tool # it should be. That will assign the correct tool number to the one that is in place.
The spindle parameters are for the stock motor but driven by a cheap AC to DC motor control.
The lathe is configured with home switches at 'min' Z (tailstock end of lathe) and 'max' X (largest diameter position for cross slide)
