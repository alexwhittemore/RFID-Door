RFID Door Lock
==============

This project centers around a [Parallax RFID reader](http://www.parallax.com/tabid/768/ProductID/114/Default.aspx) and an Arduino for reading 125khz RFID tags as a means of authentication for opening a deadbolt. The main PCB holds the barebones Arduino-compatible clone, a socket for a Digi Xbee module for communication with a home automation network or a computer, and power circuitry. It has connectors for the RFID module, a standard hobby servo to actuate the deadbolt, and a magnetic reed switch to sense whether or not the door is presently closed. It also has a header which breaks out all power connections, such that external 5V and 3.3V sources can be used.

RFID Reader
-----------

The Parallax RFID Reader above from Grand Idea Studio and Parallax is used. Both SOUT and /ENABLE are connected to the Arduino, and it is intended that a software serial port will be used (since the hardware serial is used elsewhere for communication and programming). Given this configuration, the RFID header is pin-compatible with the [new RFID reader/writer from Parallax](http://www.parallax.com/Store/Accessories/CommunicationRF/tabid/161/ProductID/688/List/0/Default.aspx?SortField=ProductName,ProductName) should that be desired in the future.

Arduino
-------

A barebones Arduino is implemented with a PDIP AVR socket (ATMega8/168/328 compatible) and an external 16MHz crystal. Arduino pins 0,1 are used for a hardware UART connection to the Xbee module. Pins 2,3 are connected to RFID SOUT and /ENABLE, respectively, usable either as GPIO or software serial. Pin 5 is used to drive the servo motor signal. AD 0 is used to sense the door open magnetic reed switch. Pull up and pull down resistor footprints are included, to be populated in accordance with the use of a normally open or normally closed reed switch. 

Additionally, a reset switch and an ISP programming header are included for users who may wish to program the chip either without the Arduino environment or simply without removing the chip from the board.

Xbee
----

A socket is included for use with a Digi Xbee module. These should all be pin-compatible across series 1/2/pro units as well as 2.4GHz/900MHz, and only the power, reset, and serial pins are utilzed. Installing this optional module allows for remote programming (I think?) as well as communication with the rest of a home automation network or a central computer. Ideas for this feature are: dynamic updating of the access list, access logging, internet-based access interface (unlock the door from your smart phone!). A reset button for the Xbee is also implemented, as is necessary for remote updating.

Level Translation
-----------------

The Xbee module is 3.3v and the Arduino is 5v, so level translation on the UART lines is necessary. The method used in the Xbee shield for Arduino is a series 10kOhm resistor on the Arduino Tx/Xbee Rx line such that the ESD diodes in the Xbee aren't overwhelmed with 20mA out of the Arduino. There is no translation on the Xbee Tx/Arduino Rx, with the expectation that 3.3v will be sufficient for a logic 'High' on the Arduino. This scheme may be used for parts count/cost reduction by solder bridging SJ1 and populating R3.

Alternately, footprints are included for On Semi MC74VHC1GT126 level non-inverting buffers. These will accurately translate 3.3V inputs to 5V outputs and vice-versa, where any input 0-10V is acceptable and 'High' output = Vdd. Populate these and leave SJ1 and R3 unbridged/unpopulated for full level translation.

Finally, headers on both input and output sides of level translation are included for ease of debugging.

Power
-----

Regulators are included with appropriate bypassing for 5V and 3.3V supplies. They are cascaded - a Vin pin is provided to supply power to be regulated down to 5V, then the 3.3V regulator takes its input from this 5V line:

Vin->`7805 Regulator`--5V+USB-->`REG103`-->3.3V

Therefore, the 5V regulator may be populated, and simply won't be used if Vin isn't supplied. However, if both external 5V and 3.3V supplies are used, the 3.3V regulator MUST be unpopulated! A USB Mini-B connector is also provided to optionally supply 5V power. Note that there is NO protection of ANY kind - this is intended only for use with dumb wall chargers or CAREFUL use supplying power from a computer. That is, the 5v regulator MUST NOT have power to it, or there could be damage!

Additionally, a REG103 from Burr-Brown (TI) is used as the 3.3V regulator. It's not a conventional part in the hobbyist spectrum (It's not used by Sparkfun/Dangerous Prototypes/OpenPilot/anyone else) - I just happened to have some. But they're inexpensive and highly available on Digikey and Mouser.

Software
--------

I haven't written it yet. But if you get to it before I do, it's a pretty standard mishmash of the SoftwareSerial library, some demo code for the RFID reader, and standard UART for the Xbee.
