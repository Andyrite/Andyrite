from cbpi.api import *
import time

@actor
class SeriesGPIOActors(CBPiActor):
    """
    Plugin to activate GPIO pins in a series with adjustable timing.
    """

    # Configurable properties: GPIO pins and timing
    gpio_pins = Property.Text("GPIO Pins", configurable=True, default_value="17,18,27", description="Comma-separated list of GPIO pins to activate in sequence.")
    timing_interval = Property.Number("Timing Interval (mins)", configurable=True, default_value=1, description="Time in minutes for each GPIO pin to stay on.")

    def __init__(self, cbpi, id, props):
        super().__init__(cbpi, id, props)
        
        # Parse the GPIO pins from the configuration
        self.gpio_pin_list = [int(pin.strip()) for pin in self.gpio_pins.split(",")]

    def start(self):
        """
        Called when the actor is started.
        """
        self.is_running = True
        self.activate_sequence()

    def activate_sequence(self):
        """
        Activate each GPIO pin in sequence with timing interval.
        """
        # Convert timing interval from minutes to seconds
        timing_seconds = int(self.timing_interval) * 60

        for pin in self.gpio_pin_list:
            if not self.is_running:
                break  # Exit if stopped
            GPIO.setup(pin, GPIO.OUT)
            GPIO.output(pin, GPIO.HIGH)
            time.sleep(timing_seconds)  # Wait for the specified interval
            GPIO.output(pin, GPIO.LOW)  # Turn off pin after interval

    def stop(self):
        """
        Called when the actor is stopped. Stops the sequence.
        """
        self.is_running = False
        # Ensure all pins are off when stopping
        for pin in self.gpio_pin_list:
            GPIO.output(pin, GPIO.LOW)
        self.cbpi.app.logger.info("Series GPIO Actors stopped.")

    def cleanup(self):
        """
        Clean up the GPIO pins when the plugin is disabled.
        """
        for pin in self.gpio_pin_list:
            GPIO.output(pin, GPIO.LOW)
        self.cbpi.app.logger.info("Series GPIO Actors cleaned up.")

