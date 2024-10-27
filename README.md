
gpio_pins and timing_interval: These properties allow you to set the GPIO pins and timing interval (in minutes) from the CraftBeerPi interface.
start Method: This method is called when the actor is activated. It calls activate_sequence, which loops over the GPIO pins, activating each one for the specified time.
activate_sequence Method: Sequentially activates each GPIO pin for the configured interval, using time.sleep() for delay.
stop Method: Stops the sequence and ensures all GPIO pins are deactivated.
cleanup Method: Ensures all GPIO pins are cleaned up when the actor stops, preventing potential GPIO pin issues.
Setup Notes
GPIO Setup: Make sure to set up the GPIO mode correctly, for example, GPIO.setmode(GPIO.BCM) if not already defined in the main CraftBeerPi config.
Error Handling: You may wish to add error handling around GPIO operations to handle any runtime GPIO 
