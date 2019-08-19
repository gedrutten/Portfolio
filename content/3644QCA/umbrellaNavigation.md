---
title: "Umbrella Navigation"
date: 2019-07-22T22:42:10+10:00
image: "/img/3644QCA/umbrella/thumbnail.jpg"
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

