# notes

## More reference links
* [online guid generator](https://guidgenerator.com/)

## Create fzpz part file
```
$ cd part
$ zip -u ../testPart.fzpz *.svg *.fzp
```

### Structure and naming

The fzpz part file is an ordinary zip file containing
* part.«part-name».fzp
* svg.breadboard.«breadboard-image-name».svg
* svg.icon.«icon-image-name».svg
* svg.pcb.«pcb-image-name».svg
* svg.schematic.«schematic-image-name».svg

part.«part-name».fzp is an xml file that conatins (in part) a "views" element wrapping four "«view»View" elements («view» is icon, breadboard, schematic, pcb).  Each "«view»View" element contains a "layers" element with an "image" attribute in the form "«view»/«view-image-name».svg".  IE The svg.«view».«view-image-name».svg image file is referenced as «view»/«view-image-name».svg.

* fzp file elements : connectors ¦ connector ¦ views ¦ «view»View ¦ p
  * attributes
    * layer="«view»"
    * svgId="«connector_pin_id»"
      * id attribute of the element in the svg file for the specified view of the containing connector that defines where the pin will be highlighted shown (red) and hightlighted (blue).  To connect to the pin, the outline for this element must be *touched* graphically.
    * terminalId="«connector_pin_terminal_id»"
      * id attribute of the element in the svg file for the specified view of the containing connector that defines where connections to the pin will snap to.
      * optional: When not specified, the center point of the svgId will be used.
    * Neither of the svgId or terminalId elements (in the svg file) need to be children of the «view» element.  Fritzing will use them for them for functionality in the view tabs.  They can be invisible elements (no border, no fill).  If they are NOT children of the «view» element, they will also not be exported to svg images.  If they are invisible, that does not mater.

It *seems* that having the svg id="connector«n»terminal" elements as children of the id="«view»" element is not enough to have them exported to the svg view images.  The existing rect elements were exported as empty groups (rect elment attributes were on a group element instead)

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

The terminal rectangle, without id, is duplicated inside the breadboard group.
The seems to be a lot of z order overlap, hiding parts of elements that are underneath.

dip package body is 0.27x0.4 inches, offset vertically only 0.03 inches from viewBox origin.
pins / legs extend 0.03 inch both top and bottom, with the 0.1 inch grid centre point being 0.015 inch outside of the body.
Origin of 0.1 inch grid for pin connection centre points is 0.045,0.015 from viewBox origin.

**I** don't want the pin/lead/leg graphics to extend past the 0.1 inch grid.  Stop the graphics at the centre of the breadboard holes.

## Errata

### Environment
Fedora 24
x86_64
Fritzing Version 0.9.2 (b8d2d5970658f0bed09c661c9

I have not upgraded to 0.9.3, since trying to stay with what is provided in the Fedora repos.

### defs and use
I've been explore Fritzing and creating new parts.  Been learning, finding, and working around either actual bugs or simple lack of knowledge.  This one looks worth reporting.

My background is computer programming, so I am quit comfortable using text/xml editors with the svg files for the part layers.  I am finding several cases where things that look perfectly valid viewing the svg files, or even the Fritzing view fail to export as svg images.  

While Some of that will be due to not understanding the constraints needed for Fritzing, it would be real helpful if Fritzing was consistent.  The parts look just fine in the Fritzing views, and also when exported as png or jpeg files.  However export to svg is a lot more problematic.  Content is simply dropped with no warning.  Once you know what to look for, it easy to check the generated export files in a text editor, and verify that the expected elements are simply not there.  That was the case with the issue someone else reported for text added to the schematic view.  This one is a bit more insidious.  Intending to reduce some duplication of common graphics pieces, I created some svg defs.  The associated use element exist in the exported svg file, but the defs (definitions) elements do not get carried across.

It appears that the svg export logic is only exporting the content of the view specify group element, without also pulling in the required supporting background elements.  That is not a complete answer though.  Manually added the defs, as well as the also needed xmlns:xlink attribute did not get the elements to actually show up.  Nor did moving the defs element inside the (in this case) breadboard group element, and manually adding the xmlns:xlink attribute.  That last did get the defs into the exported svg file, but viewer program (eog) did not show them.  Firefox partially displayed it, be the graphics were a bit corrupt.  I appears that generated svg file is not completely valid.

In the cases tried, the Fritzing view displayed the content correctly, but the exported svg file was invalid or incomplete.

### font size in pixels

http://forum.fritzing.org/t/units-on-font-style-in-svg-part-files-do-not-export-as-svg/3462/1

Text with (an svg) font size specified in px units does show up at the right size (or at all) using export ¦ as Image ¦ SVG.  It appears that the export software does not understand the units, and ends up exporting with a font-size of "0".  Simply removing the units from the font-size in the style attribute works around the problem, but a more general solution would be to fix the export logic.

```
grep -c -E "font-size:[0-9]+?(\.[0-9]*)?px" /usr/share/fritzing/parts/svg/core/*/*.svg | grep ":[1-9]" | wc
107 files found, although the icon files probably do not matter.  They are never exported.
grep -c -E "font-size:[0-9]+?(\.[0-9]*)?[a-z]" /usr/share/fritzing/parts/svg/core/*/*.svg | grep ":[1-9]" | wc
108 files found, although the icon files probably do not matter.  They are never exported.
```

Simple (hopefully) reproducible example.  In an empty sketch:
* find and place a TLP621
* Fit the breadboard view to the window
* File ¦ Export ¦ as Image ¦ SVG ¦ test_bb.svg
Outside of Fritzing, view the original and exported image files
* …/fritzing/parts/svg/core/breadboard/Optocoupler_TLP621_breadboard.svg
* test_bb.svg
The "TLP" and "621" text is visible in the original, but not in the export.
Use a text editor to look at the 2 files.
* In Optocoupler_TLP621_breadboard.svg, the text elements for TLP and 621 are wrapped in groups with style attributes.  Both start with "font-size:4.44444418px".
* In test_bb.svg, the matching text elements are also wrapped inside group elements, but now the style does not have a font-size entry, and there is new font-size attribute set to "0".  Changing that "0" to either "4.44444418px" or "4.44444418" gets the text to show up, although the size is wrong.

* Exit everything without saving.
* Change font-size:4.44444418px to font-size:4.44444418 in Optocoupler_TLP621_breadboard.svg.
* Repeat the find, place, fit, export, view.
* The text is visible in both the original and export.
* Viewing the export file in a text editor shows that the font-size has still been removed from the style attribute, but now the font-size attribute is set to "3.2".

As a guess, the parsing code for the svg export is attempting to convert the whole "4.44444418px" string to a number, which fails and gives 0.  It is not expecting or looking for units.

### connector terminal from view layer not exported to svg

http://forum.fritzing.org/t/connector-terminal-from-view-layer-not-exported-to-svg/3546

It appears that the svg export does not handle connector terminal rectangles that are part of the view (breadboard in this case) layer.  During export, the "rect" element is replaced by "g" element with the same attributes, with values adjusted for scaling.  For example, with a breadboard svg file containing
```
<!-- origin is the bottom row of pins on 0.1 inch grid -->
<rect width="2.7" x="0" y="0" height="2.7" id="connector0terminal"/>
```
The exported breadboard view svg file contained
```
<!-- origin is the bottom row of pins on 0.1 inch grid -->
<g width="2.16" x="0" y="0" id="connector0terminal" height="2.16"/>
```
Removing id="connector0terminal" from the rect element (and putting it elsewhere) worked correctly, exporting
```
<!-- origin is the bottom row of pins on 0.1 inch grid -->
<rect width="2.16" x="0" y="0" height="2.16"/>
```
Either of the version look and work correct in the Fritzing view.  It is just he export as SVG that gets messed up.
