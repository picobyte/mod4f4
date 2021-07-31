
# mod4f4 v0.07

A script to manage modules using git revision control and rsync.

There are other management tools that are used more often, like vortex or mod organiser 2. If those work for you, try them instead. If not, this script provides a Linux solution for manual packages manager with git revision control. In theory it could work on the mac OS x commandline, but probably some of the used tools are OS specific and may need adaptations. I cannot test mac, but I am interested.

After a clean Fallout 4 run `./mod4f4 --init', subsequent module installations should be undoable to return to this initial Fallout 4 commit, using git. I've tested this and experienced no problems, but since I obviously cannot be held accountable, advisable is to make a backup of your game directory.

This version control works for installation of most mods, but still requires the user to read the mod instructions carefully, be selective, resolve dependencies and prevent mentioned collisions. given:

## Requirements

User has knowledge of git and at least a bit of bash and rsync knowledge is useful.
Also some knowledge of Fallout 4 modules is required, and a general willingness to read documentation.

Commandline tools are installed:
tidy 7z unrar unzip git rsync sed sort uniq wc xml2 yad xmllint wget

unless you can manage modules with git on the commandline, gitk is also required and maybe git-gui.

## revision control (git)

Revision control; branches, bisecting problems, tagging, cherry-picking commits and resolving conflicts; nuf said. These subjects won't be covered here, though. The user is supposed to be familiar with these concepts or look them up. Management can be done on the commandline with git commands, or via git gui and gitk.

## module management (gitk)

To manage modules, run `./mod4f4 --gitk'. In the top left of gitk the module commits so far. Bottom commit is your clean Fallout 4 installation. After a module installation and its commit, a new dot will be added on top of it. Bottom right list shows where files were placed for that commit.

There are some exceptions but for most mods files should be placed under 'Data/'. If this is not the case, chance is that you have to use the --dest-base argument.

If files for a module weren't placed correctly you'll want to undo the commit. In that case right click on the last commit that was correct and select 'Reset master branch to here'. to fully undo the commit select the bottom option: Hard. With 'Mixed' or you can revise the commit, unselect parts of the changes e.g. via `git gui' (run in f4/) but unless you know what you're doing just undo the commit. Also reverting is not what you want for binary commits.

If you want to store a set-up but try out something else you can also create a branch or tag this commit in the mouse-rightbutton menu. 

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
That last command takes maybe 30 minutes. There are a few more things you need to set up while it finishes.

You nay want to also symlink your module download directory. This document assumes it is in `dnld/`. Also make the directory with the Fallout 4 .ini files a repository. The changes therein are listed here but not the revision control; manage that manually.

Adding the files under revision control takes a while. A `.gitignore` file in your f4/ directory excludes files from revision control, but this is inadvisable; if modules do touch those files, you're sh*t out of luck.

## installing mods using mod4f4

### Usage

```
./mod4f4 --init  # set up links and git revision control (no other arguments required).

./mod4f4 --gitk  # module management using gitk.

And for applying modules:

./mod4f4 [options..] <archive.7z|archive.rar|archive.zip> [rsync options..]

Options:
    --dest-base=Data/ or "" place the files in this directory. The default is to guess the location
                defaulting to Data/ but this may be wrong. Best is to set this parameter. Or at least
                check that it installed right or follow up modules may guess wrong hereafter.
                the --subset argument also influences this.

    --subset=RE includes only the matching module (sub)directories. RE: extended regular expression.

                Whether or not a directory's trailing slash is included in the capture matters.
                If uncaptured the directory is copied, otherwise the directory contents as rsync behaves.

    --rm        remove the extracted module directory after commit. Won't if dir was already existing.
    --no-stage  don't stage and commit (allows manual staging). This implies --keep-mod.
                Note that together with the --dry-run rsync option, no package contents are transferred.
                The filename and directoryname case-insensitivity resolution is still run, though.

    If there's Nexus information available (which may require manual html download for adult mods),
    requirements for the mod will be checked, using commit messages.

    --message=<string> commit with this specified message. This also implies skipping the nexus number query.
    --ignore-nexus-reqs=RE     ignore a module's listed, but missing nexus requirements
    --ignore-off-site-reqs=RE  ignore a module's listed, but potentially missing off-site requirements

For rsync options, see \`man rsync' or \`rsync --help'
```


### What mod4f4 does

1. The archive will be extracted; as such or in a subdirectory with the basename, dependent on the contents of the archive.
2. if there's a fomod directory, then this will be parsed to enable selections / preview / info view in FOModXML/imgui or yad.

   if not, --dest-base can be given or it is guessed where the data should be placed, based on the present directories in f4/
   and extracted folder contents. The selection to install can then be manipulated with --subset or rsync --exclude

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

./mod4f4 --subset="f4se_0_06_21/(Data|.*\.(exe|dll)|CustomControlMap.txt)" \
        --message="Name: Fallout 4 Script Extender
Author: ianpatt, behippo, plb
Version: 0.6.21
Website: https://f4se.silverlock.org/" dnld/f4se_*.7z
```

The message displayed here is used to resolve dependencies. If available this is parsed from FOMod/info.xml. but F4SE is a bit different, it resides on a different site. Many modules depend on it, however.


The --subset=.. regular expression is required here to omit src/ and .txt files. In Steam Library, make sure to launch Fallout 4 via this editor or modules may not work. Right-click on Fallout 4. Properties. "Launch Options" add `f4se_loader.exe`

```Bash
## BodySlide [19-01-2021]
./mod4f4 --dest-base="Data/Tools/" dnld/"BodySlide and Outfit Studio"*.7z
```

Create a `BodySlide` launcher in steam, using target `BodySlide.exe` and start in<br>
`"${HOME}/.steam/steam/steamapps/common/Fallout 4/Data/Tools/BodySlide"`<br>
Make sure to use proton experimental in the compatibility section.

```Bash
## Looksmenu [04-11-2020] prereq: F4SE
./mod4f4 dnld/LooksMenu\ v*.7z

# No prereqs
./mod4f4 dnld/'Addres Library-47327-1-10-163-0-1599728753.zip'

# VC 2015, 2017, 2019
./mod4f4 --dest-base="" dnld/'xSE PluginPreloader F4 0.2.4-33946-0-2-4-1606749072.zip'

# PluginPreloader
./mod4f4 dnld/'Buffout 4-47359-1-23-1-1623356612.zip'

### Mod Configuration Menu [07-12-2019] prereq: F4SE
./mod4f4 --dest-base="Data/" dnld/"Mod Configuration Menu "*.zip

# from here on I create separate branches for Fusion Girl and CBBE

cd f4/
git branch CBBE
git branch Fusion_Girl

git checkout Fusion_Girl
cd -


####################### CBBE ############################

./mod4f4 dnld/BodyTalk3.15.7z
./mod4f4 dnld/FG180REV1.7z

# no prereqs,
#./mod4f4 "dnld/UniquePlayer - Main File-3718-0-4-0.7z"

./mod4f4 --message="Name: ZaZ -Extended-Skeleton (ZeX)
Author: ZaZ
Version: 4.0
Website: https://www.nexusmods.com/fallout4/mods/36702" 'dnld/ZaZ - Extended Skeleton-36702-4-0-1606282622.7z'

#### AWKCR [05-01-2020]
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

#### AWKCR [05-01-2020]
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




```



