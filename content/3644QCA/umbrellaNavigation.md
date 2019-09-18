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

<center>
    {{< figure src="/img/3644QCA/umbrella/FT3L0TPNJBEP27XYUI.LARGE.jpg" link="/img/3644QCA/umbrella/FT3L0TPNJBEP27XYUI.LARGE.jpg" width="80%" >}}
    The circuit from the artice: https://www.instructables.com/id/EL-wire-eye-candy/#step9
</center>
    
<center>
    {{< figure src="/img/3644QCA/umbrella/ELSwitchCircuitSimplified.jpg" link="/img/3644QCA/umbrella/ELSwitchCircuitSimplified.jpg" width="80%" >}}
    This is a simplified version of the above circuit for a single EL wire channel.  It would need to be replicated for each channel.
</center>
    
<div class="row">
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/umbrella/moc3031_1.png" link="/img/3644QCA/umbrella/moc3031_1.png" width="100%" >}}
    </div>
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/umbrella/Z01-pinout.jpg" link="/img/3644QCA/umbrella/Z01-pinout.jpg" width="100%" >}}
    </div>
</div>

<center>
    {{< figure src="/img/3644QCA/umbrella/ELSwitchCircuitBredboard.jpg" link="/img/3644QCA/umbrella/ELSwitchCircuitBredboard.jpg" width="80%" >}}
    This is how we wired it up on the bredboard.
</center>
    

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

It'd be nice to create a prettier website, and possibly a better web-based communication system than the href system used above.  Even better if it could be consumed by another device, that way, if we have time to make a dedicated controller, we can.

### Building the optoisolator board

The circuit diagram above specified 330 Ohm resistors, and looking at the spec sheets for the MOC3031, it required 15mA to switch.  Given that the circuit was using a Basic Stamp II, it would have a logic level of 5v, which would make sense - 5v/330Ω = 0.01515mA.  Translating that to 3.3V logic level of the ESP family results in 3.3v/0.015mA = 220Ω resistors.  Tested that out - worked.  I did see some odd behavious that got me researching the actual specs of an ESP8266.  Turns out they're only rated for 12mA per IO pin, but it seems to be working, so I'm going with it!  The ESP32 is rated higher, so worst case, I find an ESP32 module - they're a little bit bigger, but we'd get Bluetooth back then too.

I managed to figure out a way to reduce the pcb footprint of the EL wire circuits even more - to the point that they only use 3 tracks on a bredboard - 11x3 grid spaces.  This means we can either create 9 very narrow switch circuits, or we can create 3 x 3-channel switch circuits that shouldn't take up much space at all.  

<center>
    {{< figure src="/img/3644QCA/umbrella/20190901_192839.jpg" link="/img/3644QCA/umbrella/20190901_192839.jpg" width="80%" >}}
    This is the circuit for 3 EL Wire switching channels.
</center>

We're hoping to use an ESP-12E module rather than the development module we've been using so far.  This is about the same size as the compass module, so combining this with the small footprint of the switches and we should be able to mount everything except the battery at the top of the umbrella.  This would mean we don't need to squeeze 18 EL wire cables up the centre shaft of the umbrella.  In fact, we should be able to reduce it to just the 2 wires required to deliver 5v to both the microprocessor and the EL inverter.

### Creating an app

I spoke to Paul about optimising the web page, and he suggested I look into MIT App Inventor.  App Inventor produces Android APK applications that can be run natively on Android.  So I tried it out.  Here's 2 out of the 3 app pages I've created so far...

<div class="row">
    <div class="6u 12u$(medium)">
        <center>
            {{< figure src="/img/3644QCA/umbrella/Screenshot_20190901-220644.jpg" link="/img/3644QCA/umbrella/Screenshot_20190901-220644.jpg" width="80%" >}}
            Compass view - this sends through changes to the heading so that the umbrella can light up the segment corresponding to the direction the phone is pointed.
        </center>
    </div>
    <div class="6u 12u$(medium)">
        <center>
            {{< figure src="/img/3644QCA/umbrella/Screenshot_20190901-220655.jpg" link="/img/3644QCA/umbrella/Screenshot_20190901-220655.jpg" width="80%" >}}
            Direction view - this allows the user to select which direction to send the umbrella holder.  It also make avaailable the party mode (random strobing) and the spin mode (lighting the segments in sequence).
        </center>
    </div>
</div>

\

I intend to also do a map view so that you can tap anywhere and the umbrella will respond by pointing in that direction - as the crow flies.

App Inventor uses a block-based programming language which I found pretty clunky.  Things like being able to share common communication code wasn't possible - both screens set up the same code to talk to the web server.  I couldn't get a tab-bar style navigation controller to happen either, but that could be just that I'm coming from iOS development and I'm looking for structures that don't exist in Android.  I think, perhaps I should have created a main page which offers the various sub-pages, then the user can navigate back to the home page to change function.

Creating an app and getting it installed on the phone (and having the phone connected to the dev environment live) is seamless and super easy to use.  Here is the code so far...

<center>
    {{< figure src="/img/3644QCA/umbrella/blocks_compass.png" link="/img/3644QCA/umbrella/blocks_compass.png" width="80%" >}}
    Source code for the compass view.
</center>

<center>
    {{< figure src="/img/3644QCA/umbrella/blocks_location.png" link="/img/3644QCA/umbrella/blocks_location.png" width="80%" >}}
    Source code for the direction view.
</center>

\
\

### Programming an ESP-12E Standalone Module

In order to make the smallest footprint possible, it would be ideal to use an ESP-12E module without the development circuits attached.  To do this though, I needed to figure out how to program the module using an external programmer.  I built a circuit according to this screenshot captured from the video: 3 Simple ways of programming an ESP-12 Module (https://youtu.be/37KlzYhJaQs)

<center>
    {{< figure src="/img/3644QCA/umbrella/esp_programmer_youtube.png" link="/img/3644QCA/umbrella/esp_programmer_youtube.png" width="80%" >}}
    Wiring diagram for ESP-12E module programmer
</center>

After building it, I plugged it into a serial programmer, but was unsuccessful at establishing a connection.  I tried with another programmer (specifically designed for an ESP-01) which didn't work either.  So finally, I tried with an Arduino Mega, configuring the Arduino software to use "Arduino as ISP" programmer.  This worked, but as soon as I disconnected and powered the module up from a different supply, it didn't work again.  I noticed that the Mega was getting quite hot, and had read somewhere that many serial programmers can't supply the current required for the ESP-12E.  So I wired up a separate 3.3v power supply to the ESP-Module, connecting the GND of the hom-made programmer to the GND of the external supply, and success - the programmer worked and the software survived once disconnected from the laptop (the separate power supply was just left in place).

<center>
    {{< figure src="/img/3644QCA/umbrella/working_external_programmer.jpg" link="/img/3644QCA/umbrella/working_external_programmer.jpg" width="80%" >}}
    {{< figure src="/img/3644QCA/umbrella/12E_Module_Programming_schem.png" link="/img/3644QCA/umbrella/12E_Module_Programming_schem.png" width="80%" >}}
    Successful programming of an ESP-12E module
</center>

If I removed the programmer though, the module stopped working again (the access point I'd programmed it with didn't appear).  Something in the programmer curcuit must be necessary to get the module to boot.  By disconnecting pins and restarting I reduced the necessary pins to Vcc and GND (obviously), EN, RST and GPIO15.

* RST - Pulled high with 10k resistor (Edit: not actually required)
* EN  - Pulled high with 10k resistor
* GPIO15 - Pulled low with 10k resistor

I guess these connections may be necessary for the module to start properly.  EN and RST connections aren't a problem, but we need every GPIO avaialble, so we might need to come up with a way of getting around the need for GPIO15 to be pulled to GND.

Actually, retesting with the modular riser and a simple blink test program, I was able to get it to function without RST connected, so only EN and GPIO15 need to be connected.

Running up another test in which I was blinking 3 LEDs connected to GPIO0, GPIO4 and GPIO15 (with GPIO15 still also being pulled low with a 10k resistor) confirmed that we can still use GPIO15 as an output pin to drive the EL wire.  This means we still have enough GPIO pins to run the 9 EL wires and the SDA, SCL pins of the compass.

<center>
    {{< figure src="/img/3644QCA/umbrella/confirmed_use_of_gpio15.jpg" link="/img/3644QCA/umbrella/confirmed_use_of_gpio15.jpg" width="80%" >}}
    Confirmed use of GPIO15 as a digital output pin even with it being pulled low by a 10k resistor.
</center>

According to the video above (that led to the programmer wiring), in order to reprogram an ESP-12E module in circuit, you just need to break out:

* VCC (+3.3v)
* GND
* Tx (Transmit)
* Rx (Receive)
* GPIO0
* EN

So we can start hardwiring the module to the EL Wire switching circuits once we've verified the compass.  We will still be able to reprogram the ESP as long as those 6 pins are exposed.

### Week 9 Progress

I completed the build of the 9 separate EL driver circuits arranged in 3 sets of 3.  Arranged in a triangle formation, this should bridge the stem on the top of the umbrella.  On top of that, we should be able to put the ESP and the magnetometer.  That's the plan, anyway.
<center>
    {{< figure src="/img/3644QCA/umbrella/20190918_155420.jpg" link="/img/3644QCA/umbrella/20190918_155420.jpg" width="80%">}}
    {{< figure src="/img/3644QCA/umbrella/20190918_155432.jpg" link="/img/3644QCA/umbrella/20190918_155432.jpg" width="80%">}}
    {{< figure src="/img/3644QCA/umbrella/20190918_155450.jpg" link="/img/3644QCA/umbrella/20190918_155450.jpg" width="80%">}}
</center>

I received a couple of Wemos D1mini's this week, and they're not that much larger than the ESP itself, so maybe it's worth using them instead of the module itself - that way we wont need the dedicated programmer anymore - much easier to update.  Essentially, the Wemos is the same module, but with the support components to allow for easy development - including a USB port.

<div class="row">
    <div class="6u 12u$(medium)">
        <center>
            {{< figure src="/img/3644QCA/umbrella/20190918_155655.jpg" link="/img/3644QCA/umbrella/20190918_155655.jpg" >}}
            The complete stack using a raw ESP-12E
        </center>
    </div>
    <div class="6u 12u$(medium)">
        <center>
            {{< figure src="/img/3644QCA/umbrella/20190918_155731.jpg" link="/img/3644QCA/umbrella/20190918_155731.jpg" >}}
            The completed stack using a Wemos module - not much bigger, but much easier to work with!
        </center>
    </div>
</div>


\

Looking at the pinouts though, it seems that some of the GPIO aren't connected - the ones on the end of the module - opposite the antenna.  We need those IO pins in order to get our full complement of 11 GPIO, so I needed to test that they were still accessible.  I just hooked up a multimeter and ran a modified version of the blink.ino example code to test that these pins could be written to.  It will mean though that we'll have to solder directly to the module to access them as they haven't been broken out as part of the Wemos module.

<div class="row">
    <div class="6u 12u$(medium)">
        <center>
            {{< figure src="/img/3644QCA/umbrella/ESP8266-ESP-12E-chip-pinout-gpio-pin.png" link="/img/3644QCA/umbrella/ESP8266-ESP-12E-chip-pinout-gpio-pin.png" >}}
            Basic ESP-12E module pinouts. Image: <a href="https://randomnerdtutorials.com/esp8266-pinout-reference-gpios/">Random Nerd Tutorials</a>.
        </center>
    </div>
    <div class="6u 12u$(medium)">
        <center>
            {{< figure src="/img/3644QCA/umbrella/ESP8266-WeMos-D1-Mini-pinout-gpio-pin.png" link="/img/3644QCA/umbrella/ESP8266-WeMos-D1-Mini-pinout-gpio-pin.png" >}}
            Wemos pinouts - notice that GPIO6 - GPIO11 aren't available on the standard pins. Image: <a href="https://randomnerdtutorials.com/esp8266-pinout-reference-gpios/">Random Nerd Tutorials</a>.
        </center>
    </div>
</div>


### Week 10 progress

<div class="row">
    <div class="6u 12u$(medium)">
        <center>
            {{< figure src="/img/3644QCA/umbrella/20190918_155749.jpg" link="/img/3644QCA/umbrella/20190918_155749.jpg" >}}
            MPU-9250 Magnetometer / Accelerometer / Gyroscope module.
        </center>
    </div>
    <div class="6u 12u$(medium)">
        <center>
            {{< figure src="/img/3644QCA/umbrella/20190918_155824.jpg" link="/img/3644QCA/umbrella/20190918_155824.jpg" >}}
            MAG3110 Magnetometer module.
        </center>
    </div>
</div>

\

These magnetometers are causing me grief!  After first finding that the module I had didn't actually have a compass (magnetometer), I ordered another, an MPU-9250 based device which has a magnetometer as well as an accelerometer and a gyroscope.  Eventually I found the right libraries and got it talking to the ESP, however the numbers didn't seem right, and after testing several different codebases, some including calibration procedures, I still wasn't getting numbers that consistently responded to points of a compass.

So I tried the compass that Vee bought from Jaycar - this module was purely a magnetometer and as such was significantly smaller.  Somewhere along my research, I thought I didscovered that it only worked when horizontal, so I'd dismissed this module, but looking at the specs again now, I can see that it is a 3 axis magnetometer and the libraries seem to be able to give you a relative heading that incorporates all 3 axis.  Hooking it up to the ESP though still produced odd figures.

Looking again at the spec sheets (Jaycar is woefully deficient in this regard, especially considering they're a retailer - a lot of googling was needed), I discovered that this module has all the components for voltage and signal level conversion for an Arduino.  So this module is really set up to run, and communicate at 5 volts, not 3.3 that the ESP family use even though the MAG3110 chip that it uses is designed for 3.3v operation.  SO I tried the module on a standard Arduino Uno and got reasonably consistent values that seemed to correspond to degrees from North.

I then tried the MPU9250 module on the Arduino too, but that one still didn't make any sense.

Perhaps we could use the 5v power rail to power the magnetometer, but that would also result in signals of 5v on the STL and SDA I2C bus, which might toast the Wemos.  

Not sure what to do from here, but the Micro:bit is looking mighty appealing again!




