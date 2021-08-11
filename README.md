
# mod4f4 v0.08

A script to manage modules using git revision control and rsync.

There are other management tools used more often, like vortex, mod organiser 2; also wrye bash seems to work on Linux for some. If those work for you, try them instead. This script provides a Linux solution for manual packages manager with git revision control. But it is 'as-is', no support.

After a clean Fallout 4 run `./mod4f4 --init', subsequent module installations should be undoable to return to this initial Fallout 4 commit, using git. I've tested this and, after adding a suitable gitignore file, haven't experienced problems, but since I obviously cannot be held accountable, make a backup of your game directory.

This version control works for installation of most mods, but still requires the user to read the mod instructions carefully, be selective, resolve dependencies and prevent mentioned collisions. given:

## Requirements

You use Linux OS and know how to install tools if they're missing.
Vortex, mod organiser 2 and wrye bash do not work for you.
User has knowledge of git and at least a bit of bash and rsync knowledge is useful.
Also some knowledge of Fallout 4 modules is required, and a general willingness to read documentation.

Commandline tools are installed:
tidy 7z unrar unzip git rsync sed sort uniq wc xml2 yad xmllint wget

unless you can manage modules with git on the commandline, gitk is also required and maybe git-gui.

Also required is rust, and in the submodule FOModXML a 'cargo build --release' needs to be run.
See also https://sr.ht/~rjck/FOModXML/

## What won't work:

Using this scheme along with another mod manager. Not sure about all, but be warned by the name Vortex. Don't even try it with this setup.

## unsure:

Repacking game files. I'm guessing this may include some files in the .gitignore file. For those you're on your own.


## revision control (git)

Revision control; branches, bisecting problems, tagging, cherry-picking commits and resolving conflicts; nuf said. These subjects won't be covered here, though. The user is supposed to be familiar with these concepts or look them up. Management can be done on the commandline with git commands, or via git gui and gitk.

## module management (gitk)

To manage modules, run `./mod4f4 --gitk'. In the top left of gitk the module commits so far. Bottom commit is your clean Fallout 4 installation. After a module installation and its commit, a new dot will be added on top of it. Bottom right list shows where files were placed for that commit.

There are some exceptions but for most mods files should be placed under 'Data/'. If this is not the case, chance is that you have to use the --dest-base argument.

If files for a module weren't placed correctly you'll want to undo the commit with a git reset --hard (right click on the last corect commit in gitk, and select 'Reset master branch to here'. to fully undo the commit select the bottom option: Hard. With 'Mixed' or you can also revise the commit, unselect parts of the changes e.g. via `git gui' (run in f4/) but unless you know what you're doing just undo the commit. Reverting can work but if you run into conflicts you'll probably have to reset hard instead. Sometimes 'git mv' on the commandline can also work.

If you want to store a set-up but try out something else you can also create a branch or tag a commit in the mouse-rightbutton menu. 

## file transfer (rsync)

Rsync has options for finetuning transfer and to prevent modules to have an effect outside the game directory. Arguments after the package to install are passed to rsync, to allow exclusion of some files.

## Initialize

clone the repository outside your Fallout 4 directory, where you do have sufficient space.
```Bash
git clone https://github.com/picobyte/mod4f4.git
cd mod4f4
chmod +x mod4f4
./mod4f4 --init
```

You may want to also symlink your module download directory. This document assumes it is in `dnld/`. Also make the directory with the Fallout 4 .ini files a repository and link it as ini here. The changes therein are listed here but not the revision control; manage that manually.


## installing mods using mod4f4

### Usage

```
./mod4f4 --init     Set up links and git revision control: add a .gitignore file with all Fallout 4 files
                    that should not be touched. These files are made read-only. The list excludes some
                    configuration files which are put under revision control instead, in a first commit
                    'Clean Fallout 4 game'.

./mod4f4 --gitk     module management using gitk.

And for applying modules:

./mod4f4 [options..] <archive.7z|archive.rar|archive.zip> [rsync options..]

Options:
    --dest-base=Data/ or "" place the files in this directory. The default is to guess the location
                defaulting to Data/ but this may be wrong. Best is to set this parameter. Or at least
                check that it installed right or follow up modules may guess wrong hereafter.

    --rm        remove the extracted module directory after commit. Won't if dir was already existing.
    --no-stage  don't stage and commit (allows manual staging). This negates --rm.
                Note that together with the --dry-run rsync option, no package contents are transferred.
                The filename and directoryname case-insensitivity resolution is still run, though.

    If there's Nexus information available (which may require manual html download for adult mods),
    requirements for the mod will be checked, using commit messages.

    --message=<string> commit with this specified message. This also implies skipping the nexus number query.
    --ignore-nexus-reqs=RE      ignore a module's listed, but missing nexus requirements
    --ignore-off-site-reqs=RE   ignore a module's listed, but potentially missing off-site requirements
                                (RE: extended regular expression)

For rsync options, see \`man rsync' or \`rsync --help'
```


### What mod4f4 does

1. The archive will be extracted, dependent on the contents of the archive, as such or in a subdirectory with the basename.
2. if there's a fomod directory, then this will be parsed to enable selections / preview / info view in FOModXML/imgui or yad.

   if not, --dest-base can be given or it is guessed where the data should be placed, based on the present directories in f4/
   and extracted folder contents. The selection to install can then be manipulated with rsync --include and --exclude.

3  chosen or matched data will be rsynced to this location in Fallout 4.
4. case-insensitivity duplicates for directories and files will be resolved. Names adapt to the earlier tracked filenames.
5. Added and changed files are staged and committed. the commit contains information to enable
   dependency checks

Do verify that the location of placement was correct.

Always read the relevant documentation per mod. Modules have dependencies, or collisions, and order is important. This script does not resolve this. In below list I considered dependencies, and where unrestrained, based the order on the module's last update (or its first appearance).

As a last step, after the installation is done, run the Bodyslide steam executable you created and batch build, for all the models.

## Module installation

```Bash
 ## F4SE

./mod4f4 --dest-base="" \
        --message="Name: Fallout 4 Script Extender
Author: ianpatt, behippo, plb
Version: 0.6.21
Website: https://f4se.silverlock.org/" dnld/f4se_*.7z --exclude f4se_whatsnew.txt --exclude f4se_readme.txt
```

The message, included in the commit, is used to resolve dependencies. If available this is parsed from FOMod/info.xml, but F4SE is a bit non-standard.

Somewhere in a reddit it is stated that f4se_loader.exe works via Launch Options. But this seems not working. Fallout 4 starts without a working F4SE. The solution that works is renaming. To know that f4se is working check that F4SE is listed in the version (settings from main menu).
```Bash
cd f4
# a check to make sure we're not overwriting the game exe, unless there has been an update.
if [ -n "$(exiftool Fallout4.exe | egrep -i '(Bethesda Softworks|ZeniMax Media Incorporated)')" ]; then
  mv Fallout4.exe F4.exe
fi
[ -e F4.exe ] && [ -n "$(cmp f4se_loader.exe Fallout4.exe)" ] && cp f4se_loader.exe Fallout4.exe
echo -e -n "[Loader]\n\rRuntimeName=F4.exe\n\r" > Data/F4SE/f4se.ini
cd -
```


```Bash
## BodySlide
./mod4f4 --dest-base="Data/Tools/" dnld/"BodySlide and Outfit Studio"*.7z
```

Create a `BodySlide` launcher in steam, using target `BodySlide.exe` and start in<br>
`"${HOME}/.steam/steam/steamapps/common/Fallout 4/Data/Tools/BodySlide"`<br>
Make sure to use proton experimental in the compatibility section.

```Bash
## Looksmenu prereq: F4SE
./mod4f4 dnld/LooksMenu\ v*.7z

# No prereqs
./mod4f4 dnld/'Addres Library-47327-1-10-163-0-1599728753.zip'

# prereq: VC 2015, 2017, 2019
./mod4f4 --dest-base="" dnld/'xSE PluginPreloader F4'*.zip

# PluginPreloader
./mod4f4 dnld/'Buffout'*.zip

### Mod Configuration Menu, prereq: F4SE
./mod4f4 --dest-base="Data/" dnld/"Mod Configuration Menu "*.zip

# from here on I create separate branches for Fusion Girl and CBBE

cd f4/
git branch CBBE
git branch Fusion_Girl
git checkout CBBE
cd -

####################### Fusion Girl ############################

./mod4f4 dnld/BodyTalk3.15.7z
./mod4f4 dnld/FG180REV1.7z

./mod4f4 --message="Name: ZaZ -Extended-Skeleton (ZeX)
Author: ZaZ
Version: 4.0
Website: https://www.nexusmods.com/fallout4/mods/36702" 'dnld/ZaZ - Extended Skeleton-36702-4-0-1606282622.7z'

#### AWKCR
./mod4f4 dnld/"Armor and Weapon Keywords Community Resource v"*.rar

./mod4f4 --dest-base="Data/" dnld/OCBPC-0.3-ZeX4.zip


./mod4f4 dnld/"MTM 3B OCBP - OCBPC Physics and Collision Preset - Beta-39195-3-0Beta-1611637094.7z"

# more modules may be installed.

# run the bodyslide batch build now. and commit 

####################### CBBE ############################

cd f4/
git checkout CBBE
cd -

./mod4f4 'dnld/CBBE Reduced (with dismemberment)-15-1-0-4-1587299287.7z'

#### AWKCR
./mod4f4 dnld/"Armor and Weapon Keywords Community Resource v"*.rar

# runs

./mod4f4 --dest-base="" dnld/OCBPC-0.3-CBBE.zip
# XXX it appears file permission matters, just making files/dirs go-w made it run
find f4/Interface f4/MCM -exec chmod go-w {} \+


./mod4f4 dnld/"MTM 3B OCBP - OCBPC Physics and Collision Preset - Beta-39195-3-0Beta-1611637094.7z"

### XXX tag Mods are loadable 1

#./mod4f4 "dnld/FPE_Interface_Script_2.610.7z"

./mod4f4 dnld/Vioxsis\'\ Strap-On\'s\ of\ Fallout\ 4.7z


# more modules may be installed.

# run the bodyslide batch build now. and commit 

###

In theory something similar could work for mac OS X, but some used tools may not work or require adaptations.


```



