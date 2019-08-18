---
title: "Day 3 - Creating Amazing Things"
date: 2019-06-05T22:16:35+08:00
image: "day03/thumbnail.jpg"
Description: "Wednesday, 5th June, 2019.  Completed the workshop with Shanghai Academy of Fine Art."
draft: false
---

Wednesday, 5th June
---
[< Prev](../day2) | [Home](..) | [Next >](../day4)

Overnight I reliased that there is a little 5x5 LED display on the Micro:bit - we could use that as the patch to entertain a child.  There's also buttons and a vibration sensor.  We could use either as a trigger mechanism for an animation to entertain the child.  The wifi is terrible at the PACC too, so I downloaded the Micro:bit iPad app at the hotel room rather than try to rnu the web-based one on my laptop.  I also did a bit of research as to how to approach the problem and found a sample program on animation.  I didn't have access to a micro:bit in the hotel room, so I just had to hope that it would connect over bluetooth and we wouldn't be left with no way of programming it with no internet access at the workshop

Today we set out to create the concept we came up with yesterday.  The idea is that we have a neckpiece that the wearer can add (or remove) panels at different stages or significant moments in their life.  For instance, when they are married or have children.  Each panel will have it's own interactivity or functionality depending on the story it is trying to tell.

We wanted to achieve at least a couple of panels:

1. A panel to be worn on the back of the collar that can entertain a child being carried on the back
2. A panel in the front with lights that responds by lighting up when the wearer sings

I set up a GoPro and we got started (Coffee was delivered not long after we started... then we really got going!)

I had the idea of having an animated character on the micro:bit and adding some sort of uplifting tune to it, so I got started with connecting the micro:bit to the iPad.  I got Jingxian to help me figure out how to depict a cat in a few frames.  We ended up with a 2-frame sequence that we reversed after a few cycles.  I also needed help figuring out how to make a happy tune.  I googled it a little the night before, but didn't come up with a series of tones or anything like that.  After experimenting a bit, Vee came over to help and we came up with a couple of tones.  After fumbling around to try to find a piano app on my phone to figure out tones, we also discovered that the micro:bit app has an inbuilt piano-style keyboard!  Just tap on the frequency as you wuold tap on a colour in a lot of IDE's.

KJ, Ricco and Chi were scultping the form of the collar while I was figuring out the micro_bit.  After that, we began working on the way to respond to the wearer's voice.  We grabbed an Arduino and a microphone module and Chi put together some code that synchronised a strip of Neopixels with the amplitude of sound being detected.  The the Neopixels were stitched into a strip that would go along the top of the collar.

Then we found some cane? and a few of us had the idea of changing (or at least concealing) the wire structure that we'd built to hold the form of the neckpiece to be woven cane.  KJ was working with Danny to create moulded forms for the shoulder sections out of the thermoplastic sheets that Danny had brought as well.

This is the final product.
{{< figure src="/img/3644QCA/day03/IMG_1371.jpg" link="/img/3644QCA/day03/IMG_1371.jpg" >}}

<div class="row">
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/IMG_4466.jpg" link="/img/3644QCA/day03/IMG_4466.jpg" >}}
        Ricco being suited up
    </div>
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/IMG_4468.jpg" link="/img/3644QCA/day03/IMG_4468.jpg" >}}
    </div>
</div>

<video width="100%" controls>
    <source src="/img/3644QCA/day03/BDMV-Clip3.m4v" type="video/mp4" >
    Your browser does not support the video tag.
</video>
  
Video of animated cat LED's  

Ricco (cameraman) and Vee (editor) along with the rest of us put together a great little video so that we could present it.  

{{< youtube rBcgS5z76zA >}}

And here's the timelapse of the days creative exploration...  

{{< youtube KGYdogzdJtk >}}

</br>

#### Other things discovered today

Mengqi Jiang (Chi) isn't actually from the Shanghai Art college, she came in from Xi’an Jiaotong-Liverpool University in Suzhou which is far enough west of Shanghai that she's also staying in accommodation locally.  Apparently she found out about the workshop and applied to come and join us.  She is studying her PhD in smart textiles and how they effect the emotional response of people with chronic diseases.  At one stage she talked about threads that she works with that change colour at different temperatures - each thread has a set temperature to which it responds to based on the dye or doping that was applied to that thread.  reminds me of hypercolour from the 80's!

KJ bought her SIM card from eBay in Australia before we left.  It's a Chinese SIM still, even though she bought it from an Australian distributor.  With all the hassles in getting a SIM here, and the associated stress in not being connected (even though I still remember travel without mobile phones... this is different though - we're trying to get work done without being connected, and we're traveling in a group), this is definately something I need to look into if I ever return to China.


#### Source code from our wearable

Code from Micro:bit - this powered the animated cat that was in the child entertainment panel on the back of the collar
<div class="row">
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/IMG_1368.jpg" link="/img/3644QCA/day03/IMG_1368.jpg" >}}
        JavaScript version
    </div>
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/IMG_1369.jpg" link="/img/3644QCA/day03/IMG_1369.jpg" >}}
        Visual Language
    </div>
</div>

Code for the Arduino to drive the neopixel strip on the neckline in response to the wearer's singing

{{< highlight c >}}

#include<FastLED.h>

#define LED_PIN   9
#define NUM_LEDS   30
#define LED_TYPE  SK6812
#define COLOR_ORDER  GRB

uint8_t max_bright = 255;

CRGB leds[NUM_LEDS];

void setup() {
  Serial.begin(9600);
  LEDS.addLeds<LED_TYPE, LED_PIN, COLOR_ORDER>(leds, NUM_LEDS);
  FastLED.setBrightness(max_bright);
}

void loop() {
  int Sound = analogRead(A0);
  
  int Light = map(Sound,0,700,255,0);
  Serial.print(Sound);  
  Serial.print("\t"); 
  Serial.println(Light);  
  
  CHSV myHSVcolor(160,155,Light);

  delay(100);  
  
  fill_solid(leds, NUM_LEDS, myHSVcolor);   
  FastLED.show();         
   
}
{{< /highlight >}}

#### Images from posters around PACC
<div class="row">
    <div class="4u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/IMG_4461.jpg" link="/img/3644QCA/day03/IMG_4461.jpg" >}}
    </div>
    <div class="4u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/IMG_4463.jpg" link="/img/3644QCA/day03/IMG_4463.jpg" >}}
    </div>
    <div class="4u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/IMG_4462.jpg" link="/img/3644QCA/day03/IMG_4462.jpg" >}}
        This jewellery reminded me again of the texture of cross stitch embroidery.  The same pixellated imagery results, only this time it is through a different medium.
    </div>
</div>

</br>

#### Evening Photowalk

Later in the evening Ricco and myself went out for a bit of a photo walk...  

<div class="row">
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/P1040319.jpg" link="/img/3644QCA/day03/P1040319.jpg" >}}
        Street level photo of a scooter coming my way.
        {{< figure src="/img/3644QCA/day03/P1040323.jpg" link="/img/3644QCA/day03/P1040323.jpg" >}}
        Reflection of the red traffic light on a collection of bikes arked in the street.
    </div>
    <div class="6u 12u$(medium)">
        {{< figure src="/img/3644QCA/day03/P1040334.jpg" link="/img/3644QCA/day03/P1040334.jpg" >}}
        An open fuse box
        {{< figure src="/img/3644QCA/day03/P1040331.jpg" link="/img/3644QCA/day03/P1040331.jpg" >}}
        This definately looks like a Christian church building - it has a cross on the roof peak.  I was under the impression that Christianity was frowned on in China, but this seems to contradict that idea.
    </div>
</div>
