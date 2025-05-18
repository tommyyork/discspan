discspan
========

DiscSpan is a tool that will take that a large directory full of files and automatically generate ISOs that fit your target media. It's theoretically capable of burning the media directly, but this feature still needs some work.

author:  James S. Martin  ceedvd ( a t )  g  m a i l .com
maintainer: Richard M. Shaw  hobbes1069 ( a t ) g m a i l .com
python3 upgrade + modernization: Tommy York tommy.york (a t) g m a i l.com

This tool is geared more towards smaller files such as music and photos 
as it cannot span a single file across multiple discs.  

If you have lots of files in the gigabyte range, this utility won't be as efficient.

The tool is written in Python and uses growisofs as the burning interface.

# Requirements
ISO generation is confirmed to work on a Synology NAS (with various packages
installed via opkg, and some packages compiled and installed from source 
(like libtool) where the `-devel` package that would live in Debian's APT).
I've left submodules for these projects, some of which were surprisingly
difficult to find. For example `mkisofs` is currently a part of `schilytools`
and included here as a submodule. Installing it required some 
[extra autoconf steps involving libtool](https://www.gnu.org/software/automake/manual/html_node/Libtool-library-used-but-LIBTOOL-is-undefined.html),
but I suspect this wouldn't be an issue  problem on a full featured Linux
distribution.

Burning is untested, and relies on dbus/Hal and growisofs, though the dbus
module was upgraded to `dbus-next` from `dbus-python`. 

# Installation

There are submodules for Hal (hardware abstraction layer), and schilytools (mkisofs). You can populate these repositories with:

`git submodule update`

Once you've built or independently installed those tools, set up a venv for discspin:

`python3 -m /path/to/discspin`

Enter the directory, then activate the venv:

`source ./bin/activate`

Then install dependencies via run `pip install -r requirements.txt`

The configuration file is required and sets forth the capacity of various storage mediums. Put discpan.ini in /etc, /usr/local/etc, the same dir as discspan.py, or specify with `--config=`. See below for the default formats.

To split a folder across multiple blu-ray ISOs (dual layer, in this case):

`python discspan.py --iso-dir /volume1/backup/discspan --dir "/volume3/Lightroom/Film Backups" --config ./discspan.ini --disc-type bd-r-dual`

Burning directly is not yet tested and working, but you can get close (with dbus_next). Run `dbus_launch`, which will give you two environment variables. Set these.

```
export DBUS_SESSION_BUS_ADDRESS=unix:path=/opt/tmp/dbus-B6rOXjeoH3,guid=dc6a75978ed47c071b7ab0916829421a
export DBUS_SESSION_BUS_PID=25016
```

You will need a service definition for Hal that the dbus module can find.

# Disc Type Options

```
floppy = 1.44MiB 147456
cdr = 700M 737280000
dvd_r = 4.384G 4707319808
dvd_rw = 4.384G 4707319808
dvd_r_dl = 7.957G 8543666176
dvd_rw_dl = 7.957G 8543666176
dvd_plus_r = 4.378G 4700372992
dvd_plus_rw = 4.378G 4700372992
dvd_plus_r_dl = 7.961G 8547991552
dvd_plus_rw_dl = 7.961G 8547991552
bd-r-single =  23.3G 25018184499
bd-r-dual = 46.6G 50036368998
bd-re-single = 23.3G 25018184499
bd-re-dual = 46.6G 50036368998
bd-xl-triple = 93.1G 99965363814
bd-xl-quadruple = 119.2G 127990025420
```

# Usage

Usage: discspan.py [options]

Options:
  -h, --help            show this help message and exit
  --config=CONFIG_FILE  Location of config file.
  --start-disc=START_DISC
                        Specify disc to start with (in case of failed previous
                        burn)
  --skip-big            Skip files that are too big.
  --test                Performs a test run of the burn.
  --dir=BACKUP_DIR      Directory to backup.
  --volume-name=VOLUME_NAME
                        Name for the volume.
  --size-factor=SIZE_FACTOR
                        Specify size factor for disc capacity, i.e. 1 = 100%
  --iso-dir=ISO_DIR     Redirect iso generation to a directory. Filename will
                        be generated from volume name.
  -v, --verbose         Extended verbosity

If you don't supply a backup dir, you will be prompted.

# Notes

The discspan.ini measurements are defined in gibibyte and mebibyte.

1G is defined as 2^30 bytes.

Lots of media manufactures define their stuff in gigabytes, which is
93% less than a gibibyte.  

That's why the numbmers you see in discspan.ini may look different
than what's labeled on your media.  Feel free to tweak to your 
hearts content.

Read here:  

http://en.wikipedia.org/wiki/DVD-R

http://en.wikipedia.org/wiki/Gigabyte

----------
# Thank Yous (from the original author)
Thanks go out to Doc, Richard, Dez, Erin, Jason, Adam for their debugging, code contributions, and ideas!

Thank you to Davyd Madeley and Dieter Verfaillie for their DBUS
code examples!  Without that, I would not have figured out
how to detect the DVD drive :).

Also thanks to the #python channel on freenode.  Wonderful folks.
