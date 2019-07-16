# Fritzing Export SVG image Testing
Explore problems exporting breadboard and schematic views to svg images from Fritzing.

See the [githubio](https://mmerlin.github.io/fesTest/) page.

Environment:
* Fedora 24 x86_64
* Fritzing Version 0.9.2 (b8d2d5970658f0bed09c661c9e)

Fritzing ¦ File ¦ Export ¦ as Image ¦ SVG is dropping graphics for some parts and content.  This has been seen for both the breadboard and schematic views.  The PCB view has not been used a lot, at least for exporting, so I do not know if the problem exists there too.  For the same Fritzing documents, exporting as png or jpeg images appear correct, which points to a problem with the svg export code in Fritzing.  However, some things do work, so it also appears to be related to exactly how the parts were created.  This repository is being used to document and track testing of manipulation of the svg and fzp files used to create a fzpz part to import into Fritzing, and the actual results when placing the part, and exporting the views.

## Reference Links
* Fritzing home page http://fritzing.org/
* Fritzing forums http://forum.fritzing.org/
* Fritzing graphic standards http://fritzing.org/fritzings-graphic-standards/
* [Fritzing source code](https://github.com/fritzing/fritzing-app)
* fzp file format https://github.com/fritzing/fritzing-app/wiki/2.1-Part-file-format
  * rubber band legs
  * internal connections
  * spice modelling
  * split schematic symbols
* [connector element needs stroke to export](https://github.com/fritzing/fritzing-app/issues/3262#issuecomment-290686168)

## Status
### Basic parts

Seem to be working fine, when being created with a text / xml editor, instead of a graphics program.  As long as all of the rules / idosyncrocies are followed.
* Content to be exported needs to be children of the element specified in the fzp file path module ¦ views ¦ «view_name»View ¦ layers ¦ layer element, layerId attribute.
* Do not include units on font-size attributes
* Do not used elements specified in the fzp file path module ¦ connectors ¦ connector ¦ views ¦ «view_name»View ¦ p element, terminalId attribute for visible graphics.
* Do not use svg defs elements.
* Specified svgId elements need their own stroke width to export correctly.
