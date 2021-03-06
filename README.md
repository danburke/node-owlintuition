#node-owlintuition


This is a node.js library for the [OWL Intuition range](http://www.theowl.com) of energy monitoring and control systems. It has been tested against v2.0 and v2.1 of the Network OWL firmware.

##Installation

Installation is via npm

    npm install owlintuition

##Monitoring

Create an instance of the owl class and connect to the multicast broadcast from the Network OWL,

    var OWL = require('owl');
	var owl = new OWL();
	owl.monitor();

you can subscribe to four different event messages.

###Electricity

The first message is for electricity updates,

	
	owl.on('electricity', function( event ) {
	});
	

where you will receive an event object of the form,

	
	{"id":"443719001958",
	 "signal":
	    {"rssi":"-66",
	     "lqi":"127"},
	 "battery":"100%",
	 "channels":
	    {"0":[
	        {"current":"241.00", "units":"w"},
	        {"day":"823.49", "units":"wh"}],
	     "1":[
	        {"current":"0.00", "units":"w"},
	        {"day":"0.00", "units":"wh"}],
	     "2":[
	        {"current":"0.00", "units":"w"},
	        {"day":"0.00", "units":"wh"}]}}		

as an argument to your callback function. The channels contain data for the current reading and the daily total for each of the channels/phases on the electricity transmitter. If the transmitter only has 1 channel, the last 2 channels will always contain 0 values.

###Heating

The second message is for heating updates, and will only occur if a Intuition-c Room Monitor has been installed,	

	owl.on('heating', function( event ) {
		
	});

where you will receive an event object of the form, 

	
      {"id":443719001958,
       "signal":
          {"rssi":-66,
           "lqi":49},
       "battery":"2970mV",
       "temperature":
          {"until":1373409000,
           "zone":0,
           "current":21.25,
           "required":20}}
	

passed back as an argument to your callback function. The temperature subsection, contains the current room temperature and the required room temperature. Both values are in degrees Celsius.

###Weather

The third and last message type is for periodic local weather updates,

	owl.on('weather', function( event ) {
		
	});
	

where you will receive an event object of the form,

      {"id":443719001958,
       "code":113,
       "temperature":"26.01",
       "text":"Clear/Sunny"}
	

passed back to your callback function. The temperature is the current outside temperature for the postcode assigned to the Network OWL. The text is a textural description of the weather at that postcode.

###Solar

Finally the fourth message is for solar updates, and will only occur if an Intuition-pv system is installed,

    owl.on('solar', function(event) {
	
    });

where you will receive an event object of the form

      {"id":"443719001958",
       "current":[
          {"generating":1000,"units":"w"},
          {"exporting":730, "units":"w"}],
       "day":[
          {"generated":23000, "units":"wh"},
          {"exported":17000, "units":"wh"}]}

This contains sections for the current solar readings and the totals for the day. Both sections have generated and exported keys that contain the values for power generated by the PV system and power exported to the electricity grid.

###Unknown

There is also an error message if the module encounters a 'new' unknown message over multicast,

	owl.on('error', function( error ) {
	
	});	
	

where a Javascript Error object will be returned. The string describing the unknown message will contain both the original multicast XML packet, and the JSON translation of the message.

##Control

You can issue commands to the Network OWL by configuring the instance of the owl class with the IP address of the Network OWL device, and your UDP key. 

    owl.configure( '192.168.1.x', '11AABB' );

Each Network OWL has a unique UDP key which can be requested from The OWL customer services at customer.services@theowl.com. Commands will only be processed if a valid UDP key is used.

All control functions will return an event you can subscribe to,

    owl.on('control', function( event ) {
    
    });	

where you will receive an event object of the form,

      {"status":"OK",
       "result":"..."}
	

passed back to your callback function. Where the result is a comma seperated string containing the result passed from the Network OWL.

**Note:** Only a subset of the commands supported by the Network OWL are implemented at this time. Pull requests are always welcome.

###Version

Retrieves the version information from the device.

    owl.version();

###Uptime

Retrieves the run time of the device in days, hours, minutes and seconds.

    owl.uptime();


###Device

Manages interal device list. Allows you to view device details.

    owl.device();	

###Mac

Returns the MAC ID of this network owl.

	owl.mac();	

###Boost

Boosts the heating temperature.

    owl.boost("ON");
    owl.boost("OFF");

will turn the BOOST status ON or OFF respectively.

##Closing Connection

Stop monitoring the multicast socket, and if configured, the control socket,

    owl.stop();


# LICENSE

This software is distributed under the [MIT](http://en.wikipedia.org/wiki/MIT_License) license.

Copyright (C) 2013 Alasdair Allan <alasdair@babilim.co.uk>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
