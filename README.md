# Fritzing Export SVG image Testing
Explore problems exporting breadboard and schematic views to svg images from Frizting.

See the [githubio](https://mmerlin.github.io/fesTest/) page.

Environment:
* Fedora 24 x86_64
* Fritzing Version 0.9.2 (b8d2d5970658f0bed09c661c9e)

Fritzing export image as svg is dropping graphics for some parts and content.  This has been seen for both the breadboard and schematic views.  The PCB view has not been used a lot, at least for exporting, so I do not know if the problem exists there too.  For the same Fritzing documents, exporting as png or jpeg images appear correct, which points to a problem with the svg export code in Fritzing.  However, some things do work, so it also appears to be related to exactly how the parts were created.  This repository is being used to document and track testing of manipulation of the svg and fzp files used to create a fzpz part to import into Fritzing, and the actual results when placing the part, and exporting the views.

Reference Links
* Fritzing home page http://fritzing.org/
* Fritzing forums http://forum.fritzing.org/
