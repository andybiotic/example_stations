# Andysine's Example Stations

## Introduction
This is an illustrative station newGRF, primarily intended as a tool to help artists, designers and coders start their own station mods for OpenTTD.

No previous coding experience should be required to understand the structure and function the source, though NML can be a little daunting at first.

I have included an overview of a basic build workflow. Creating a newGRF with NMLC is a very manual process. For this reason, there may be other, more suitable tools, such as [grf-py](https://github.com/citymania-org/grf-py?tab=readme-ov-file#grf-py). I would recommend checking these out too before starting here, as it may save you some time in the long run.

## Getting Started
To get started, you will need to setup Python on your computer and install the NML compiler (NMLC). You'll need to download (or clone) this repository and then compile it using NMLC. This should produce a functional newGRF, which you can add to your game.

## Setup
This section provides an outline on how you can configure the build tools on your device. Your exact process may differ because of platform requirements or personal preference. 

**Install Python**
You'll need to install Python 3 on your device. You won't write any code in Python, but this is what NMLC uses to parse your NML code and produce the file.

I'd also recommend using a [virtual environment](https://docs.python.org/3/tutorial/venv.html) for your project, as this can avoid issues with dependencies. In this tutorial, the virtual environment is located in the folder `developer/grfenv`.

**Clone or Download the Example Stations Project**
You'll need to download this repository to a folder on your computer. In this tutorial, the project files are saved in the folder `developer/example_stations`.

**Activate Your Python Environment**
Once everything is in the right place, you'll need to active your Python environment from the command line: 
`source developer/grfenv/bin/activate`

**Navigate to your project folder**
From the virtual environment, navigate to the project folder:
`cd developer/example_stations`

**Compile the newGRF**
With everything setup, you can now instruct the build process to compile the file. 
`python build.py`

## Project Structure and Syntax
#### Setting a grfid
`src/header.nml` is used to setup and control the buold process. This uses a Python script to generate an NML file which is then used to compile the GRF, via NMLC. It is recommended that you immediately set the `grfid` to a new, unique value. If another GRF uses the same ID, both cannot be loaded into the same game.

> It's convention to use the first three bytes for the creator's initials. The fourth byte typically identies which of the author's sets this is...

#### Parsing the NML
`src/example_1.nml` contains a range of objects, with some useful properties. In the source NML generally has the following structure:

1. The spriteset is defined. These are the coordinates, bounds and offsets for the sprites on the source spritesheet. For a simple tile this will include two orientations, one facing SW/NE, the other SE/NW. 
```
spriteset (example_platform_left, "stations_1.png") {
    [  10, 10, 64, 64, -32, -34 ]
    [  80, 10, 64, 64, -32, -34 ]
}
```

2. Next, the spritelayouts are defined. For a simple rail tile this usually includes both an X and a Y variant (for the SW/NE and SE/NW orientations).

```
spritelayout platform_left_sw_ne(a) {
    ground {
        sprite: GROUNDSPRITE_RAIL_X;
    }
    building {
        sprite:         DEFAULT(0);
        xoffset:        0;
        yoffset:        1;
        zoffset:        0;
        xextent:        16;
        yextent:        5;
        zextent:        12;
        recolour_mode:  RECOLOUR_REMAP;
        palette:        PALETTE_USE_DEFAULT;
    }
}

spritelayout platform_left_se_nw(a) {
    ground {
        sprite: GROUNDSPRITE_RAIL_Y;
    }
    building {
        sprite:         DEFAULT(1);
        xoffset:        11;
        yoffset:        1;
        zoffset:        0;
        xextent:        5;
        yextent:        16;
        zextent:        12;
        recolour_mode:  RECOLOUR_REMAP;
        palette:        PALETTE_USE_DEFAULT;
    }
}
```

`sprite:` denotes the location in the spriteset, in this case either [0] or [1]. Complex tiles may have multiple sprites per orientation.

`xoffset:`, `yoffset:` is used to align the sprites. 

`zoffset:` is always 0.

`xextent:`,`yextent:` and `zextent:` are used to set the bounding boxes. Bounding boxes should almost always be larger than the sprites they contain. 

Complex, layered tiles may have sprites that overlap or obscure each other. Check out the 'Bridge Station' example to see this in more detail.

Lastly, the object is defined:

```
item(FEAT_STATIONS, example_left) {
    property {
        class:                  "XMPL";
        classname:              string(STR_NAME_STATCLASS_SINGLE_PLATFORMS);
        name:                   string(STR_NAME_STATION_EXAMPLE_PLATFORM);
        cargo_threshold:        160;
        draw_pylon_tiles:       STAT_ALL_TILES;
    }
    graphics {
        sprite_layouts: [platform_left_sw_ne(0), platform_left_se_nw(0)];
        select_tile_type: 2;
        purchase: example_platform_left;
        example_platform_left;
    }
}
```

More examples can be found in the `example_1.nml` source file.

## Graphics Workflow
This section provides a high level overview of the technical steps required to import and prepare artwork for use by NMLC and OpenTTD. 

There are certainly other ways of achieving the same results. This is just what worked for me.

**Part 1: Draw using Pixaki app on iPad**
- Import the [palette files](https://newgrf-specs.tt-wiki.net/wiki/PalettesAndCoordinates#Palettes) the Pixaki app. I used the `pal_win_png.act` palette, but the DOS palette has a few more colours, so this is probably better.
- Export your chosen sprite sheet to your computer as a .png file. At this point, the image file will not have the requisite palette applied.

**Part 2: Prepare the image using GIMP on macOS**
1. Open the .png file using GIMP on the computer.
2. Import the palette into GIMP.
![Screenshot of GIMP, importing a palette file](/resources/setup_1.png)
3. Set the colour mode to 'Indexed'.
![Screenshot of GIMP, importing a palette file](/resources/setup_2.png)
4. Convert the image to the imported palette.
![Screenshot of GIMP, importing a palette file](/resources/setup_3.png)
5. Export the image as a png. You may want to just overwrite the original file at this point and move it to the project folder.
6. Compile the newGRF (as above).

