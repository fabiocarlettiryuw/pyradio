# PyRadio

Command line internet radio player.

Ben Dowling - [https://github.com/coderholic](https://github.com/coderholic)

## Table of contents

* [Requirements](#requirements)
* [Installation](#installation)
* [Command line options](#command-line-options)
* [Controls](#controls)
* [Config file](#config-file)
* [About Playlist files](#about-playlist-files)
* [Player detection / selection](#player-detection-selection)
* [Player default volume level](#player-default-volume-level)
* [Debug mode](#debug-mode)

## Requirements

* python 2.6+/3.2+
* MPV, MPlayer or VLC installed and in your path.
* [socat](http://www.dest-unreach.org/socat/) (if you wish to use MPV)

## Installation

The best way to install **PyRadio** is via a distribution package, if one exists (e.g. *Arch Linux* and derivatives can install [pyradio-git](https://aur.archlinux.org/packages/pyradio-git/) from AUR).

In any other case, and since **PyRadio** is currently not available via pip, you will have to [build it from source](build.md).

## Command line options

```
$ pyradio -h

usage: pyradio [-h] [-s STATIONS] [-p [PLAY]] [-u USE_PLAYER] [-a] [-ls] [-l]
               [-scd] [-ocd] [-d]

Curses based Internet radio player

optional arguments:
  -h, --help            show this help message and exit
  -s STATIONS, --stations STATIONS
                        Use specified station CSV file.
  -p [PLAY], --play [PLAY]
                        Start and play.The value is num station or empty for
                        random.
  -u USE_PLAYER, --use-player USE_PLAYER
                        Use specified player. A comma-separated list can be
                        used to specify detection order. Supported players:
                        mpv, mplayer, vlc.
  -a, --add             Add station to list.
  -ls, --list-playlists List of available playlists in config dir.
  -l, --list            List of available stations in a playlist.
  -scd, --show-config-dir
                        Print config directory location and exit.
  -ocd, --open-config-dir
                        Open config directory with default file manager.
  -d, --debug           Start pyradio in debug mode.
```

The following options can also be set in **PyRadio**'s [configuration file](#config-file):

* **-s** - parameter **default_playlist** (default value: **stations**)
* **-p** - parameter **default_station** (default value: **-1**)
* **-u** - parameter **player** (default value: **mpv, mplayer, vlc**)

## Controls

```
Up/Down/j/k/PgUp/PgDown   Change station selection.
g                         Jump to first station.
<n>G                      Jump to n-th / last station.
Enter/Right/l             Play selected station.
r                         Select and play a random station.
Space/Left/h              Stop/start playing selected station.
-/+ or ,/.                Change volume.
m                         Mute.
v                         Save volume (not applicable for vlc).
o s R                     Open / Save / Reload playlist.
DEL,x                     Delete selected station.
?                         Show keys help.
Esc/q                     Quit.
```

## Config file

**PyRadio** upon its execution tries to read its configuration file (i.e. *~/.config/pyradio/config*). If this file is not found, it will be created. If an error occurs while parsing it, an error message will be displayed and ***PyRadio*** will terminate.

The file contains parameters such as the player to use, the playlist to load etc. It is heavily commented (as you can see [here](pyradio/config)), so that manual editing is really easy. The best practice to manually edit this file is executing ***PyRadio*** with the **-ocd** command line option, which will open the configuration directory in your file manager, and then edit it using your preferable text editor.

The file can also be altered while **PyRadio** is running. This process is transparent to the user; **PyRadio** will save the file before exiting (or in case Ctrl-C is pressed) if needed (e.g. if a config parameter has been changed during its execution).

## About Playlist files

**PyRadio** reads the stations to use from a CSV file, where each line contains two columns, the first being the station name and the second being the stream URL.

**PyRadio** will by default load the user's stations file (e.g. *~/.config/pyradio/stations.csv*) to read the stations from. If this file is not found, it will be created and populated with a default set of stations.

**Tip:** If you already have a custom *stations.csv* file, but want to update it with **PyRadio**'s default one, you just rename it, run **PyRadio** (so that the default one get created) and then merge the two files.


**Note:** Older versions used to use ***~/.pyradio*** as default stations file. If this file is found, it will be copied to use's config directory (e.g. ***~/.config/pyradio***) and renamed to ***stations.csv*** or if this file exists, to ***pyradio.csv***. In this case, this file will be the default one.

### Specifying a playlist to load (command line)

**PyRadio** will normally load its default playlist file, as described above, upon its execution. A different file can be loaded when the **-s** command line option is used.

The ***-s*** option will accept:

* a relative or absolute file name.
* the name of a playlist file which is already in its configuration directory.
* the number of a playlist file, as provided by the ***-ls*** command line option.

Examples:

To load a playlist called "***blues.csv***", one would use the command:

```
pyradio -s /path/to/blues.csv
```

If this file was saved inside ***PyRadio***'s configuration directory, one could use the following command:

```
pyradio -s blues
```

To use the playlist number, one would execute the commands:

```
$ pyradio -ls
Playlists found in "/home/user/.config/pyradio"
  1. hip-hop
  2. party
  3. stations
  4. huge
  5. blues
  6. rock
  7. pop
$ pyradio -s 5
```

**Note:** The default playlist to load can also be set in **PyRadio**'s [configuration file](#config-file), parameter **default_playlist** (default value is ***stations***).

### Managing playlists (within PyRadio)

Once **PyRadio** has been loaded, one can perform a series of actions on the current playlist and set of playlists saved in its configuration directory.

Currently, the following actions are available:

One thing you may want to do is remove a station from a playlist, e.g. when found that it not longer works. You can do that by pressing "***DEL***" or "***x***".

Then, when this is done, you can either save the modified playlist, by pressing "***s***", or reload the playlist from disk, by pressing "***R***". A modified playlist will automatically be saved when **PyRadio** exits (or Ctrl-C is pressed).

Finally, opening another playlist is also possible. Just press "***o***" and you will be presented with a list of saved playists to choose from. These playlists must be saved beforehand in **PyRadio**'s configuration directory.

While executing any of the previous actions, you may get confirmation messages (when opening a playlist while the current one is modified but not saved, for example) or error messages (when an action fails). Just follow the on screen information, keeping in mind that a capital letter as an answer will save this answer in **PyRadio**'s configuration file for future reference.

## Player detection / selection

**PyRadio** is basically built around the existence of a valid media player it can use. Thus, it will auto detect the existence of its supported players upon its execution.

Currently, it supports MPV, MPlayer and VLC, and it will look for them in that order. If none of them is found, the program will terminate with an error.

MPV will be used only when the [socat](http://www.dest-unreach.org/socat/) multipurpose relay is also installed.

Users can alter this default behavior by using the ***-u*** command line option. This option will permit the user either to specify the player to use, or change the detection order.

Example:

```
pyradio -u vlc
```
will instruct **PyRadio** to use VLC; if it is not found, the program will terminate with an error.

```
pyradio -u vlc,mplayer,mpv
```
will instruct **PyRadio** to look for VLC, then MPlayer and finaly for MPV and use whichever it finds first; if none is found, the program will terminate with an error.

**Note:** The default player to use can also be set in **PyRadio**'s [configuration file](#config-file), parameter **player** (default value is ***mpv, mplayer, vlc***).

## Player default volume level

MPV and MPlayer, when started, use their saved (or default) volume level to play any multimedia content. Fortunately, this is not the case with VLC.

This introduces a problem to **PyRadio**: every time a user plays a station (i.e restarts playback), even though he may have already set the volume to a desired level, the playback starts at the player's default level.

The way to come around it, is to save the desired volume level in a way that it will be used by the player whenever it is restarted.

This is done by typing "***v***" right after setting a desired volume level.

### MPV

MPV uses profiles to customize its behavior.

**PyRadio** defines a profile called "**[pyradio]**" in MPV's configuration file (e.g. *~/.config/mpv/mpv.conf*). This profile will be used every time playback is started.

Example:

    volume=100

    [pyradio]
    volume=50

### MPlayer

MPlayer uses profiles to customize its behavior as well.

**PyRadio** defines a profile called "**[pyradio]**" in MPV's configuration file (e.g. *~/.mplayer/config*). This profile will be used every time playback is started.


Example:

    volume=100

    [pyradio]
    volstep=1
    volume=28

## Debug mode

Adding the ***-d*** option to the command line will instruct **PyRadio** to enter *Debug mode*, which means that it will print debug messages to a file. This file will always reside in the user's home directory and will be named *pyradio.log*.

In case of a bug or a glitch, please include this file to the issue you will [open in github](https://github.com/coderholic/pyradio/issues).
