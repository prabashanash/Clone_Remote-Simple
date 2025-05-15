# IR Remote Learning and Playback

This Arduino code allows an Arduino board to learn and playback Infrared (IR) remote control signals. It utilizes the `IRremoteESP8266` library for IR communication and the `LowPower` library for power saving.

## Features

* **IR Signal Learning:** Records IR signals from a remote control and stores them based on button presses on the Arduino.
* **IR Signal Playback:** Replays the learned IR signals when the corresponding button is pressed again.
* **Hold Functionality:** Differentiates between a short press (playback) and a long press (learning) of the input buttons. Holding a button for longer than a defined timeout will trigger the learning mode for that button.
* **Power Saving:** Implements low power mode to conserve energy when not actively learning or playing back signals. The Arduino wakes up via an interrupt on a designated pin.
* **Visual Feedback:** Uses an LED to indicate when the board is in learning mode.
* **Support for Known and Unknown Protocols:** Can learn and replay signals from various IR protocols, including unknown (raw) signals.

## Hardware Requirements

* Arduino board (e.g., Arduino Uno, Nano)
* IR Receiver module (e.g., VS1838B)
* IR Emitter LED
* Push buttons (at least 6, connected to digital pins 2 through 7)
* LED for indicator (optional, connected to digital pin 13)
* Jumper wires for connections

## Connections

* **IR Receiver:**
    * VCC to Arduino 5V
    * GND to Arduino GND
    * OUT to Arduino digital pin 10 (`irRecPin`)
* **IR Emitter LED:**
    * Anode (longer leg, often with a resistor in series) to Arduino digital pin 11 (`irSendPin`)
    * Cathode (shorter leg) to Arduino GND
* **IR Receiver Ground Control (Optional but Recommended):**
    * Connect a digital output pin (Arduino digital pin 12, `irRecGndPin`) to the GND pin of the IR receiver. This allows for disabling the receiver during transmission to avoid interference.
* **Wake Up Button (Optional):**
    * One side of a button to Arduino digital pin 2 (`wakeUpPin`)
    * The other side to Arduino GND (configure the pin with `INPUT_PULLUP` if needed, or use an external pull-up resistor if connecting to VCC)
* **Input Buttons:**
    * Connect push buttons between Arduino digital pins 2 through 7 (inclusive) and GND. The internal pull-up resistors are used for these pins.
* **LED Indicator (Optional):**
    * Connect the LED (with a current-limiting resistor) to Arduino digital pin 13 (`ledIndicatorPin`).

## Software Requirements

* Arduino IDE installed
* Install the following libraries through the Arduino Library Manager:
    * **IRremoteESP8266** by Armin Joachimsmeyer (Make sure to install the version compatible with your board)
    * **LowPower** by Rocketscream

## Setup

1.  **Install Libraries:** Open the Arduino IDE, go to `Sketch` > `Include Library` > `Manage Libraries...` and search for and install the "IRremoteESP8266" and "LowPower" libraries.
2.  **Connect Hardware:** Wire up the components as described in the Connections section.
3.  **Upload Code:** Copy and paste the provided code into a new Arduino sketch in the Arduino IDE and upload it to your Arduino board.

## Usage

1.  **Initialization:** After uploading, the Arduino will initialize and print "Initialized and working" to the Serial Monitor (at 115200 baud). It will then enter a low-power state.
2.  **Wake Up:** To interact with the board, trigger a low signal on the `wakeUpPin` (digital pin 2). If you don't have a dedicated wake-up button, any change on the input pins (2-7) might also wake the board due to the interrupt configuration.
3.  **Learning IR Signals:**
    * Press and **hold** one of the input buttons (connected to digital pins 2 through 7) for longer than the defined `holdTimeout` (default is 1000 milliseconds).
    * The indicator LED (if connected to pin 13) will turn ON, indicating that the board is in learning mode for that specific button.
    * Point your IR remote control at the IR receiver and press the button on the remote whose signal you want to learn.
    * The Arduino will attempt to decode and store the received IR signal associated with the held input button.
    * The details of the received IR signal (protocol, address, command, or raw data) will be printed to the Serial Monitor.
    * The indicator LED will turn OFF once the signal is received.
4.  **Playing Back IR Signals:**
    * Press and **release** (short press) the same input button that you used to learn the IR signal.
    * The Arduino will transmit the stored IR signal using the IR emitter LED.
    * If the learned signal was from an unknown protocol (raw), it will be replayed as raw data. Otherwise, it will be sent using the detected protocol.
5.  **Low Power Mode:** After processing a button press (either learning or playback), the Arduino will go back into a low-power sleep state, waiting for another wake-up interrupt.

## Pin Definitions

* `wakeUpPin`: Digital pin 2 (used for waking the Arduino from low power mode)
* `irRecPin`: Digital pin 10 (connected to the IR receiver's data pin)
* `irSendPin`: Digital pin 11 (connected to the IR emitter LED)
* `irRecGndPin`: Digital pin 12 (optional control pin for the IR receiver's ground)
* `ledIndicatorPin`: Digital pin 13 (optional LED for indicating learning mode)
* `startOfInputPins`: Digital pin 2 (the first pin used for input buttons)
* `endOfInputPins`: Digital pin 7 (the last pin used for input buttons)

## Constants

* `holdTimeout`: Defines the duration (in milliseconds) a button needs to be held to trigger learning mode (default: 1000 ms).

## Notes

* The code stores one learned IR signal for each input button (connected to pins 2 through 7).
* The `irRecGndPin` is used to ground the IR receiver during transmission, which can help prevent the receiver from picking up its own transmitted signal.
* Ensure that the `IRremoteESP8266` library is correctly installed for your specific Arduino board.
* The `EXCLUDE_EXOTIC_PROTOCOLS` and `NO_LED_FEEDBACK_CODE` preprocessor definitions are used to save program memory. You can comment these out if you need support for more protocols or want the library's built-in LED feedback (though this code implements its own LED feedback).
* The code prints debugging information to the Serial Monitor, which can be helpful for troubleshooting.
* The wake-up functionality relies on a low-level interrupt on `wakeUpPin`. Ensure your wake-up mechanism provides a clear low signal.
