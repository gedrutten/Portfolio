---
title: "Markdown Reference Page"
date: 2019-05-11T12:10:20+10:00
draft: true
---


## Header text (inside 3 dashes "---")
title: "Centrifugal Fan"
description: "A study in modelling for injection molding and designing around existing components.  This project also allowed me to explore the fundamentals of centrifugal fan design."
date: "2018-11-30T12:00:00+10:00"
image: "centrifugalFan/thumbnail.jpg"

# Heading 1
## Heading 2
### Heading 3
#### Heading 4

## Text effects
Using a single enter at the end of a line 
-> means nothing


Using a double space at the end of a line prior to an enter is an  
-> in-paragraph enter


Using a double enter at the end of a line indicates a 

-> new paragraph

Superscript<sup>TM</sup>
Subscript<sub>2</sub>
**Bold**
_Italics_
~~strikethrough~~

Making a reference to a footnote [^fn2].  
Making a reference to an out of order footnote [^fn1].  

Comments: 

[//]: # (This is a comment that wont be seen in the output)


Dot points (Must be preceeded by a blank line)

+ point 1
+ point 2
+ point 3

Numbered list

2. point 1
1. point 2  
    Use 4 spaces to add indented line between points
3. point 3


## Tables
Key | Action
---|---
←↑↓→   | Use your arrow keys to move
1-8    | Select between 8 different camera views
[, ]   | Tilt the camera (only selected cameras)
p, \\  | Pan the camera (only selected cameras)


## Links
### Internal Link
Play the game [here](/ac2021/)

### External Link
Shortlisted for 2019 [Lexus Design Awards](https://discoverlexus.com/experiences/lexus-design-award-2019)  
Shortlisted for 2019 [Lexus Design Awards][LDA] using reference style links


## Code
small sections of `code are encased in single backticks`

Whereas large chunks are encased in 3 backticks:
``` C
for (i=0; i<10; i++) {
    do something;
}
```

## Blockquotes

> Someone said something very important


## Youtube links
{{< youtube unENuDS6_hg >}}


## Figures
{{< figure src="/img/portfolio/solarStill/assembly.jpg" >}}


## Responsive Columns
### 2-column
<div class="row">
    <div class="6u 12u$(medium)">
        Column 1 content
    </div>
    <div class="6u 12u$(medium)">
        Column 2 content
    </div>
</div>

### 3-column
<div class="row">
    <div class="4u 12u$(medium)">
        Column 1 content
    </div>
    <div class="4u 12u$(medium)">
        Column 2 content
    </div>
    <div class="4u 12u$(medium)">
        Column 3 content
    </div>
</div>

[LDA]: https://discoverlexus.com/experiences/lexus-design-award-2019
[^fn2]: Some bibliographical reference.
[^fn1]: Another out of order footnote
