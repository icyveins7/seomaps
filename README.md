# seomaps

seomaps is a Python wrapper around Folium for those who just want a quick and dirty way of seeing simple data on a map, with steps required to set it up particularly for an offline node running Windows.

## Folium Dependencies

Many of the plugins, css, js files used by Folium are by default downloaded via online means. As such, for offline use this repo has added submodules for some dependencies with particular versions checked out. The versions for each plugin are as specified [here](https://python-visualization.github.io/folium/plugins.html) and [here](https://python-visualization.github.io/folium/modules.html). The folium version tested is 0.12.1.

## Prerequisites

Styles, sprites, icons default to [osm-liberty](https://github.com/maputnik/osm-liberty). Clone the repository to retrieve the sprites (really the only folder that we'll need).
Using osm-liberty cause it's prettier.

Install Docker. We'll be using [Tileserver GL](https://tileserver.readthedocs.io/en/latest/installation.html) via Docker.

Obtain or purchase planet tiles (.mbtiles format) from [Maptiler](https://data.maptiler.com/downloads/tileset/osm/).

For python modules, the following should suffice

```bash
pip install numpy beautifulsoup4 folium
```

## (New) Usage with Vector Tiles

tileserver-gl-light may be easier to set up, but only works with vector tiles. Since this method
doesn't go through Docker, loading tiles is faster; however, it currently lacks an easy way to style
the raster tiles.

First, install Node and install the light version via 

```bash
npm install -g tileserver-gl-light
```

Then, clone this repository, the submodules and copy the .mbtiles file into this folder (same as with raster tiles).

```bash
git clone https://github.com/icyveins7/seomaps.git
git submodule update --init --recursive
```

When finished, simply run the tileserver via (for example)

```bash
tileserver-gl-light osm-2017-07-03-v3_6_1-planet.mbtiles --verbose
```

To use the map, create an object with 

```python
svmap = SVecMaps()
svmap.plot()
```

## Usage with Raster Tiles

Clone this repository and then pull all the submodules.

```bash
git clone https://github.com/icyveins7/seomaps.git
git submodule update --init --recursive
```

Copy the .mbtiles file into this repository folder as we will be assuming it resides there.

Run the batch file 

```bash
dockerStartCmd.bat
```

to start the docker container that loads the tiles and styles them according to osm-liberty.

To use the map, create an object with 

```python
smap = SMaps()
smap.plot()
```

## Offline Usage

First get the image on the online computer (with Docker on it).

```bash
docker pull maptiler/tileserver-gl
```

On the online computer, save the image to a single tar file by running

```bash
docker image save --output tileserver-gl.tar maptiler/tileserver-gl
```

and then copy the file over to the offline computer.
On the offline computer (also with Docker running), run the command:

```bash
docker image load -i tileserver-gl.tar
```

You should now be able to run the command from the previous section

```bash
dockerStartCmd.bat
```

and it should find the existing image.

## Notes on Directory Usage and Docker Installation

The previous batch file for setting environment variables appears to not work. Docker will continue to install its main files into the standard C:\Program Files area.
As of now, the batch file in old commits is not recommended. Just move the vhdx images file via the below commands.

Tested: As of 13/12/2020, using the commands found here [https://dev.to/kimcuonthenet/move-docker-desktop-data-distro-out-of-system-drive-4cg2] we move the problematic images
vhdx file (the distro file is never that large) by doing the following (copied from the link):

1) Stop Docker

2) Run
```bash
wsl --shutdown
```

3) Export temporary tar.
```bash
wsl --export docker-desktop-data D:\sometempdir\docker-desktop-data.tar
```

4) Unregister, this also automatically deletes the vhdx file in C:\Users\SeoNotebook\AppData\Local\Docker\wsl
```bash
wsl --unregister docker-desktop-data
```

5) Make a permanent new folder (here it is E:\docker-desktop\data), and re-import the tar file
```bash
wsl --import docker-desktop-data E:\docker-desktop\data D:\sometempdir\docker-desktop-data.tar --version 2
```

## Troubleshooting

1) Specified path for "fonts" does not exist (/usr/src/app/node_modules/tileserver-gl/fonts).

Run the default tileserver command without a config.json but with verbose output; this is contained in 

```bash
dockerStartCmdRaw.bat
```

Observe the output of the config.json, and amend the 'root' in the custom config.json accordingly. Likely this will be /app/node_modules/tileserver-gl/fonts i.e. without /usr/src.

