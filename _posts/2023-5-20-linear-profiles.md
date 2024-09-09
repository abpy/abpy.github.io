---
layout: post
title: "Making Linear Camera Profiles with dcpTool"
author: "Aaron"
---
A linear profile is a camera profile that does not add a contrast curve or other look to an image. When used in a raw converter (adobe ACR / Lightroom, etc) it will show the colors and tones as the sensor and calibration rendered them, without any default adjustment already applied.

A linear profile is essential for tasks like reproducing artwork, scanning negatives and slides, or getting a flat base to use for a LUT. It can also be used as a starting point for photo editing as an alternative to the default look.

This post will cover how to convert an adobe standard profile into a linear profile by using dcptool to convert between dcp and xml. These instructions are written for mac, but it should be similar on windows.

## Using dcpTool
Camera Profiles are stored in the .dcp format based on dng.
### Getting dcpTool

dcpTool is a command line program that runs in the terminal. It can convert a dcp file to xml and the xml back to dcp. While command line programs aren't as intuitive as a gui, this guide will try to make it as painless as possible.

Download dcpTool from [dcptool.sourceforge.net/Introduction](https://dcptool.sourceforge.net/Introduction.html)

You can place the dcptool folder anywhere you want, you do not need to install anything.
The executable files are in the folder /Binaries/macOS or /Binaries/Windows.

### Find a profile
1. Find the adobe standard profile for your camera
2. Copy this file to a different folder

Path for mac
```
/Library/Application Support/Adobe/CameraRaw/CameraProfiles/Adobe Standard/
```

Path for windows
```
\ProgramData\Adobe\CameraRaw\CameraProfiles\Adobe Standard\
```

### Converting dcp to xml
Usage instructions can also be found here [dcptool.sourceforge.net/Usage](https://dcptool.sourceforge.net/Usage.html)

To run the command in terminal you need: (all separated by spaces)
```
<path to the dcpTool executable> [options] <path to dcp file> <path to save xml file to>
```

1. open terminal
2. drag and drop the dcptool executable into terminal
3. type "-d "
4. drag and drop the dcp file
5. drag and drop the dcp file again but delete ".dcp" and type ".xml"
6. press enter

It will write an xml file to the same folder you placed the dcp in

## Edit the xml to make it linear
Using a text editor we will change several xml tags

The top of the file will look like this
```
<?xml version="1.0" encoding="UTF-8"?>
<dcpData>
  <ProfileName>Adobe Standard</ProfileName>
```
Change ProfileName to Adobe Standard Linear

```
  <ProfileName>Adobe Standard Linear</ProfileName>
```

Find the end of the `<HueSatDeltas>` tags and the start of the `<LookTable>` or `<ToneCurve>` tags.
(some cameras may not have all or any of these tags)

example:
```
  </HueSatDeltas2>
  <LookTable hueDivisions="36" satDivisions="8" valDivisions="16">
    <Element HueDiv="0" SatDiv="0" ValDiv="0" ...
```
Delete the LookTable and ToneCurve
from `<LookTable...` to `...</LookTable>` and `<ToneCurve...` to `...</ToneCurve>`

Replace with the linear curve
```
  <ToneCurve Size="2">
    <Element N="0" h="0.000000" v="0.000000"/>
    <Element N="1" h="1.000000" v="1.000000"/>
  </ToneCurve>
```

Change DefaultBlackRender to 1

This tells adobe not to automatically recalculate the black level, but to use the level stored in the raw file.
```
  <DefaultBlackRender>1</DefaultBlackRender>
```

Finally save the xml with a new name

## Convert xml to dcp
Same as above but use option "-c "

Place the xml file first then the path for the new dcp

Move the new dcp file to:

```
Mac:
/Users/<user>/Library/Application Support/Adobe/CameraRaw/CameraProfiles/

Windows:
\Users\<user>\AppData\Roaming\Adobe\CameraRaw\CameraProfiles\
```

The new profile should appear in the Profile Browser under Adobe Raw.
