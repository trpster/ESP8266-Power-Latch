This is for battery operated ESP8266 applications where the various sleep 
modes won't work.  Instead, the device remains unpowered until a button is
pressed (or a Hall Effect sensor detects a magnet).  The button turns on
the power to the ESP8266 using Q2; the ESP8266 code immediately makes GPIO-0
low, which keeps the Q2 turned on if the button is no longer being pressed, by 
turning on Q1 and subsequently Q3.  Then the ESP8266 does whatever it is
supposed to do, and when the operation is complete it raises GPIO-0 which
turns off Q1 and Q3, and if the button is no longer being pressed, the device
becomes unpowered.

In the application init.lua launches wifi.lua that turns GPIO-0 low and
establishes a connection with the router that has reserved the IP address
so dhcp isn't required (which consumes more time than defining a static
IP address).  wifi.lua then launches doorbell.lua that registers itself with
an MQTT broker, publishes the "on" message, then raises GPIO-0 which turns
the power off (if the button is no longer being pressed).

The circuit uses a LM317T voltage regulator to produce 3.1V frm a 3.7v
lithium-ion battery.  The divider network for the regulator results in a
constant drain of 0.56 mA.  If a battery with a higher voltage were used,
the button and Q2 could be moved to the other side of the regulator so 
the 'off' drain would be zero, but starting at just 3.7v would result in 
a smaller drop in the battery charge before needing to be recharged.
As it is, the 3.7v should remain high enough to keep the circuit working
for over 200 days.   

