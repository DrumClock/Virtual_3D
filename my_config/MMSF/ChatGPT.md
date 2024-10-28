**!! ------ ChatGPT ----- !!**

Here’s the complete process for implementing the G-code command MOVE_FILAMENT into Klipper,
which checks the STOP_ON_ENDSTOP parameter and does not execute the movement if this parameter is not provided.

1. Prepare the Environment
Ensure you have Klipper installed and have access to its configuration files.

2. Create a New Core
Create a new file for the core in the klipper/klippy/ directory. For example: extruder_move.py.
3. Implement the Core

Open the file extruder_move.py and insert the following code:

```

from klipper import config
from klipper import gcode
from klipper import printer

class ExtruderMove:
    def __init__(self, printer, endstop_pin):
        self.printer = printer
        self.endstop_pin = endstop_pin

        # Register the G-code command
        self.printer.register_gcode_command("MOVE_FILAMENT", self.move_filament)

    def move_filament(self, params):
        extruder_name = params.get('EXTRUDER', None)
        stop_on_endstop = params.get('STOP_ON_ENDSTOP', None)  # Changed to None

        if extruder_name is None:
            raise ValueError("EXTRUDER must be specified.")
        
        # Check if the STOP_ON_ENDSTOP parameter is provided
        if stop_on_endstop is None:
            raise ValueError("STOP_ON_ENDSTOP must be specified.")

        # Convert the value to int
        stop_on_endstop = int(stop_on_endstop)

        if stop_on_endstop not in [1, -1]:
            raise ValueError("STOP_ON_ENDSTOP must be 1 (for stop on activation) or -1 (for stop on deactivation).")

        extruder = self.printer.get_extruder(extruder_name)

        # Logic for filament movement
        if stop_on_endstop == 1:  # Stop on activation of endstop
            extruder.set_position(1)  # Move forward
            while True:
                if self.printer.get_endstop(self.endstop_pin).is_triggered():
                    extruder.set_position(0)  # Stop movement
                    break

        elif stop_on_endstop == -1:  # Stop on deactivation signal
            extruder.set_position(-1)  # Move backward
            while True:
                if not self.printer.get_endstop(self.endstop_pin).is_triggered():
                    extruder.set_position(0)  # Stop movement
                    break

def load_config(config):
    endstop_pin = config.get('endstop_pin', None)
    
    if endstop_pin is None:
        raise ValueError("endstop_pin must be specified.")
    
    return ExtruderMove(config.get_printer(), endstop_pin)
    
```
    
4. Modify Klipper Configuration
Open your Klipper configuration file, usually printer.cfg.
Add a new block for the new core:

```
[extruder_move]
endstop_pin: <YOUR_ENDSTOP_PIN>
```

5. Restart Klipper
After making all changes, restart Klipper to load the new core.

6. Testing
For testing, use the G-code command:

```
MOVE_FILAMENT EXTRUDER=<config_name> STOP_ON_ENDSTOP=1  ; Stop on activation
MOVE_FILAMENT EXTRUDER=<config_name> STOP_ON_ENDSTOP=-1 ; Stop on deactivation

```

Verification:

1. Check if the extruder responds correctly to G-code commands.
2. Ensure that the extruder movement stops when the endstop is activated or deactivated.
3. Try running the command without the STOP_ON_ENDSTOP parameter to ensure a proper error message is generated.
