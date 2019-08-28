---
title: "Umbrella Navigation"
date: 2019-07-22T22:42:10+10:00
image: "umbrella/thumbnail.jpg"
Description: "A post-tour collaboration with Vee"
draft: false
---
[Home](..)

### The beginnings

At a Maker's club meetup, Vee came along and started working on her umbrella navigation device.  She'd come prepared with EL wire, white, red and green, along with lots of handheld power supplies, a Rasperry Pi, heaps of other components and a clear umbrella.  After smashing off the handle with a hammer, she went to work threading the white EL wire through the perimeter of the umbrella.  It looked cool.  The the discussion came on how to do the direction bit.

She had a compass, and a Raspberry Pi, so those could be used to control it, but switching EL wire on and off can't be done with a Raspberry Pi... or any kind of microcontroller for that matter.  Not straight from the board anyway.  Looking around, Sparkfun has an [EL Light Sequencer](https://www.sparkfun.com/products/12781), but it looked quite large and hard to conceal in an umbrella.  On top of that, ideally we'd need 10 channels instead of just the 8:

+ The outside white ring (could then flash it or do whatever)
+ The red "Stop" ring
+ 8 direction indicators that would be represented by green arrows, one in each umbrella panel (thereby indicating N, NE, E, SE, S SW, W, NW).  

<center> {{< figure src="/img/3644QCA/umbrella/umbrellaLighting.png" link="/img/3644QCA/umbrella/umbrellaLighting.png" caption="Green, red and perimeter lighting.  Bright green arrows show direction (currently straight ahead).  Red would indicate 'Stop'." width="75%" >}} </center>

Worst case, we could just indicate 4 directions, forward, left right and back, and then use a 5th channel to control stop, but it'd be better if we could show the half-angles too (all 8 points of the compass).  

A quick search came up with an [instructible](https://www.instructables.com/id/EL-wire-eye-candy/#step9) where someone had made their own switching curcuit using an opto-coupler and a triac for each channel.  I think we could use this idea to build our own.

Vee had also bought an electronic compass, and it would be awesome if the umbrella didn't care what direction it was held... it'll always point to the right direction.  In fact, if you twirled it, it could always show the arrow closest to the direction you're meant to travel.  That would be cool.

The interface for the compass was I2C.  There would be support for that on the Raspberry Pi, and doing some reseach, there's a library written to use I2C on Arduino's too.  The Raspberry Pi would probably be overkill for this project.  Vee was talking about having informational messages streamed into the top of the umbrella - this way the sound could be relatively low as it would be quite directional onto the intended recipient, but not loud enough that everyone has to put up with it - that might need the power of a RasPi!  Both the Arduino and the RasPi would have enough digital I/O to support the switching of the EL wire.

By this stage, I'd gotten pretty keen on seeing this project make it to completion, so I offered to help out by building the switching circuits and having a look at the code.  That's probably all within my capabilities and would be much better than having a manual switch to control the display - which is where the discussion was headed.

Thinking about it on the way home, it makes sense to use a micro:bit.  There's a compass built in, there's bluetooth and there's plenty of I/O.  We could use the bluetooth to connect to a mobile phone.  For the demo, this could simply be a way to control the direction (through a phone, computer, or maybe that Raspberry Pi - Bluetooth serial connection), but ultimately, it could be connected to a navigation app that uses the phone's GPS to guide a user while they walk.

{{< figure src="/img/3644QCA/umbrella/20190819_193054.jpg" link="/img/3644QCA/umbrella/20190819_193054.jpg" >}}
Trying the concept out by threading some EL into the hem of an umbrella.

### Testing the EL Wire Switching

Having recieved the triacs and triac-optocouplers, we bredboarded up the example circuit and tested it using an arduino.  Here's a video of it running the sample blink project.

<video width="100%" controls>
    <source src="/img/3644QCA/umbrella/BlinkTest.mp4" type="video/mp4">
    Your browser does not support the video tag.
</video>

{{< figure src="/img/3644QCA/umbrella/FT3L0TPNJBEP27XYUI.LARGE.jpg" link="/img/3644QCA/umbrella/FT3L0TPNJBEP27XYUI.LARGE.jpg" width="100%" >}}
The circuit from the artice: https://www.instructables.com/id/EL-wire-eye-candy/#step9

{{< figure src="/img/3644QCA/umbrella/ELSwitchCircuitSimplified.jpg" link="/img/3644QCA/umbrella/ELSwitchCircuitSimplified.jpg" width="100%" >}}
This is a simplified version of the above circuit for a single EL wire channel.  It would need to be replicated for each channel.

<div class="row">
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/umbrella/moc3031_1.png" link="/img/3644QCA/umbrella/moc3031_1.png" width="100%" >}}
    </div>
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/umbrella/Z01-pinout.jpg" link="/img/3644QCA/umbrella/Z01-pinout.jpg" width="100%" >}}
    </div>
</div>


{{< figure src="/img/3644QCA/umbrella/ELSwitchCircuitSimplified.jpg" link="/img/3644QCA/umbrella/ELSwitchCircuitSimplified.jpg" width="100%" >}}
This is how we wired it up on the bredboard.

\

### Getting Some Code Done

The aim of the day was to get the compass working, verify it with some LED's and start working on communications.  The current architecture is to use an ESP32 as the microcontroller - it has more than enough IO pins, and has bluetooth and wifi.  Hopefully we can create a bluetooth connection to a phone or something to make it operate as an accessory to the phone.

Initial connection of the LED's to the ESP-32 dev board confirmed that I had all the libraries working and could scroll through some LED's.  I then started working on connecting the compass... well, what I thought was a compass.  After doing all the research, it turned out the MPU6050 based "compass" I had was an accelerometer and a gyroscope - it wasn't a compass at all!  No wonder I couldn't make any sense of the values I was getting when trying to correlate them to a direction.

So I checked what Jaycar has for their compass since I knew Vee had one of those.  It is a compass that talks over I2C, but it only works when it is flat, if it's tilted, it starts producing erroneous data.  I decided to order an MPU9250 sensor which includes a 3 axis magnetometer - hopefully it'll be here in a week or so.

In the meantime, I decided to continue with gravity as my "North" and test the I2C communication between the sensor and the microcontroller.  I hooked it up and had it responding to the tilt of the sensor - so we can change the code once we've got a compass to respond accordingly.  The video below shows the results of testing the led indicators responding to the rotation of the 6050 module.

<video width="100%" controls>
    <source src="/img/3644QCA/umbrella/tilttest.mp4" type="video/mp4">
    Your browser does not support the video tag.
</video>

Next I started looking at the communications.  We had the idea of this ultimately being connected to Google Maps through an API or something similar (Couldn't find an API for Google Maps, maybe Waze?).  This would be best done over bluetooth - allowing the phone to continue normal wifi/4G communications, and still talk to the umbrella.  

With the exhibition however, connecting to waze or the likes wont be of much benefit though since the exhibition will be held in a small space.  Using bluetooth though also requires that we write the server side software, or connect to the appropriate API.  Another option was to create a second "remote control" device that provides the bluetooth server and provides the direction to the umbrella remotely.  Since both the ESP32 and the smaller ESP12 (WeMos) have the ability to run a web server which would simply require a wifi-enabled device to interact with the umbrella.  Perhaps this is a better start for the time being?

Of course, the micro:bit has a compass and it has both bluetooth and wifi.  My main issue with the micro:bit is the connector to the IO pins.  Overall, if we can get away with a Wemos and the separate compass module, we will have a very small device footprint.  The micro:bit has a large connector that's required to get to all the IO pins.  Since we pretty much have to have the controller board at the top of the umbrella (it would be very difficult to run 9 digital lines up the inner shaft of the umbrella), the smaller the controller, the better.

{{< highlight html >}}
HTTP/1.1 200 OK
Content-type: text/html
<!DOCTYPE HTML>
<html>
<head>
<style>
.button {
  background-color: #4CAF50;
  border: none;
  color: white;
  padding: 25px 52px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 26px;
  margin: 10px 5px;
  cursor: pointer;
}
</style>
</head>
<body>
<center>
<input type="button" class="button" onclick="location.href='NN'" value="North">
<br>
<input type="button" class="button" onclick="location.href='NW'" value="NW">
<input type="button" class="button" onclick="location.href='NE'" value="NE">
<br>
<input type="button" class="button" onclick="location.href='WW'" value="West">
&nbsp&nbsp&nbsp
<input type="button" class="button" onclick="location.href='EE'" value="East">
<br>
<input type="button" class="button" onclick="location.href='SW'" value="SW">
<input type="button" class="button" onclick="location.href='SE'" value="SE">
<br>
<input type="button" class="button" onclick="location.href='SS'" value="South">
<center>
</body>
</html>
{{< /highlight >}}

The code snippet from within the ESP code results in the following web page on the left and the log in the serial monitor on the right.

<div class="row">
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/umbrella/directionWebpage.jpg" link="/img/3644QCA/umbrella/directionWebpage.jpg" width="100%" >}}
    </div>
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/umbrella/directionTerminal.jpg" link="/img/3644QCA/umbrella/directionTerminal.jpg" width="100%" >}}
    </div>
</div>
