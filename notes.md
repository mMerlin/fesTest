# notes

## More reference links
* [online guid generator](https://guidgenerator.com/)

## Create fzpz part file
```
$ cd part
$ zip -u ../testPart.fzpz *.svg *.fzp
```

## file paths
```
~/.config/Fritzing/bins/*.fzb
~/.config/Fritzing/parts/svg/user/«view»/*.svg
~/.config/Fritzing/parts/user/*.fzp
/usr/share/fritzing/bins/*.fzb
/usr/share/fritzing/bins/*/*.fzb
/usr/share/fritzing/parts/svg/«category»/«view»/*.svg
```

## Finding files
```
$ find /usr/share/fritzing/ -iname "*dip_relay*" -type f -print
```

What is the scaling of a breadboard view export image as svg?  A wire across the gap in a breadboard (0.3 inches?) is
x1='61.3195' y1='86.4' x2='61.3195' y2='64.8'
giving
21.6 units high, or 72 units / inch

lm358 breadboard view has all of the connector«n»pin and connector«n»terminal rect elements outside of and *before* the breadboard group.  They have a fill of #8c8c8c, but the are overlaid by later graphics, and so are invisible.  To either EOG or Fritzing.  Size is 3x4.3400002 for the connector pins
The terminal rectangles are 3x3 and align to (overlapping) the outer end of the connector rectangles.
The viewBox is setting to 100 px / inch
The positioning / spacing puts the centres 0.3 inch apart, and the complete rectangles are included in the viewBox, with a height of 0.33 inches.  Both the connector and terminal extend beyond the 0.1 inch multiple distance.

The terminal rectange, without id, is duplicated inside the breadboard group.
The seems to be a lot of z order overlap, hiding parts of elements that are underneath.

dip package body is 0.27x0.4 inches, offset vertically only 0.03 inches from viewBox origin.
pins / legs extend 0.03 inch both top and bottom, with the 0.1 inch grid center point being 0.015 inch outside of the body.
Origin of 0.1 inch grid for pin connection center points is 0.045,0.015 from viewBox origin.

**I** don't want the pin/lead/leg graphics to extend past the 0.1 inch grid.  Stop the graphics at the center of the breadboard holes.
