<p align="center">
  <img width="600" src="logo.png">
  <h3 align="center">cut SMT stencils from Gerber files</h3>
</p>
<br><br>

[![GPL](https://img.shields.io/badge/license-GPL-blue)](https://github.com/pmonta/gerber2graphtec/blob/master/LICENSE)
[![Python](https://img.shields.io/badge/language-Python3-orange)](https://www.python.org)
[![GitHub Release](https://img.shields.io/badge/release-v0.2-brightgreen)](https://github.com/pmonta/gerber2graphtec/releases)

## About

SMTCut, previously called **gerber2graphtec**, is a tool for cutting accurate SMT stencils on a **Graphtec** or **Silhouette** vinyl cutter from Gerber files.<br>

Techniques include separately drawn line segments (no complex paths), antibacklash, drag-knife-angle "training", and multiple passes.<br>

This produces stencils usable down to approximately **0.5 mm pitch** (QFP/QFN) and **0201** discrete components, or perhaps even slightly better; but it is slower than normal cutting.


## Getting Started

To be able to run this software, make sure that you have installed the following dependencies:

- [gerbv](http://gerbv.geda-project.org)
- [pstoedit](http://www.calvina.de/pstoedit/)
- [libusb](https://libusb.info)

#### Install on Mac OSX:
Make sure you already have installed [Homebrew](https://brew.sh/) and simply run the following commands:

```
brew install gerbv
```
```
brew install pstoedit
```
```
brew install libusb
```
Also make sure that you've installed the libusb1 python package or install it via
```
pip3 install libusb1
```

## Usage

In general there are two different cases to distinguish depending on your operating system. Please note that this process can take a few minutes before the cutter starts to cut!

### Linux
E.g. a solderpaste gerber file (paste.gbr), and default settings:

```
gerber2graphtec paste.gbr >/dev/usb/lp0
```

Or a more elaborate command line with linear map (to correct spatial miscalibration) and multiple passes with different speeds and forces:

```
gerber2graphtec --offset 3,4 --matrix 1.001,0,-0.0005,0.9985 --speed 2,1 --force 5,25 paste.gbr >/dev/usb/lp0
```

### Mac OSX and Windows
With Mac OS X or Windows, the file2graphtec script can take the place of /dev/usb/lp0. But keep in mind that the software is tested on Mac OS X but not on Windows!

E.g. a solderpaste gerber file (paste.gbr), and default settings:

```
python3 gerber2graphtec paste.gbr > tmpfile
python3 file2graphtec tmpfile
```

Or a more elaborate command line with linear map (to correct spatial miscalibration) and multiple passes with different speeds and forces:

```
python3 gerber2graphtec --offset 3,4 --matrix 1.001,0,-0.0005,0.9985 --speed 2,1 --force 5,25 paste.gbr > tmpfile
python3 file2graphtec tmpfile
```

## Known issues
Due to a bug inside ghostscript, you may need to downgrade to version < 9.26 or you might encounter repeating output --> [Issue17](https://github.com/pmonta/gerber2graphtec/issues/17)

On Mac OSX this can be done by uninstalling ghostscript with:

```
brew uninstall --ignore-dependencies ghostscript
```

and reinstalling the version 9.25

```
brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/b0b6b9495113b0ac5325e07d5427dc89170f690f/Formula/ghostscript.rb
```

## Calibration
To achieve the best results, one should run different calibrations, which can be found in the test folder after the blade is set. The following example shows how to calibrate the cutter on Mac OSX but the commands are also applicable to other operating systems (make sure you run these files from the main folder).

```
python3 tests/test_forces.py > tmpfile
python3 file2graphtec python3 file2graphtec tmpfile  
```

## GUI

An optional GUI has been provided by [jesuscf](https://github.com/jesuscfv). It allows interactive selection of the input Gerber file, parameters, and cutting operations.

To use it, run the following command:

```
python3 g2g_gui.py
```

## Tips & Tricks
You may want to have your CAM tool shrink the paste features by about 2 mils before exporting to gerber.  The craft-cutter knife, when cutting thin mylar, seems to spread out the geometry by about this amount.  I'd suggest using mylar with thickness between about 3 and 5 mils; the IPC-recommended thicknesses for fine-pitch stencils are approximately in this range.  (Typical inexpensive laser-transparency sheets happen to be just right, being somewhere between 3.5 and 4.3 mils.)  You may have to experiment with the cutting speeds and forces for best quality with your materials.

Fedora 17 has gerbv-2.6.0 in its supplied package list, so no issue there.  For Fedora-like systems, "yum install gerbv pstoedit tkinter".

On some Linux distributions, permissions on /dev/usb/lp0 are restricted by default.  To fix this, add yourself to the lp group and then log out and log back in:

```
sudo usermod -a --group lp your_userid
```

## Additional Information

These pages have hints on usage (e.g. on Windows), materials, performance, calibration, etc:

- [pmonta.com](http://pmonta.com/blog/2012/12/25/smt-stencil-cutting/)
- [dangerousprototypes.com](http://dangerousprototypes.com/forum/viewtopic.php?f=68&t=5341)
- [hackaday.com](http://hackaday.com/2012/12/27/diy-smd-stencils-made-with-a-craft-cutter/)

## Credits

Thanks to the authors of robocut and graphtecprint for protocol documentation:

- [robocut](http://gitorious.org/robocut)
- [graphtecprint](http://vidar.botfu.org/graphtecprint)
- [graphtecprint](https://github.com/jnweiger/graphtecprint)

Also thanks to this web page (Cathy Sexton) for inspiration:

- [idleloop.com](http://www.idleloop.com/robotics/cutter/index.php)

Her cutter seems to be quite a bit better than mine was out of the box.
With Silhouette's default software my 0.5 mm pitch pads were distorted to the point of unusability.
