
# mod4f4
A script to manage modules using git revision control and rsync.

There are other management tools that are used more often, like vortex or mod organiser 2. If those work for you, try them instead. If not, this script provides a Linux commandline solution for manual packages manager with git revision control. In theory it could work on the mac OS x commandline, but may need adaptations because not all command work the same (I cannot test it but am interested).

After a clean Fallout 4 `./mod4f4 --init', subsequent module installations should be undoable to return to this initial Fallout 4 commit, using git. I've tested this and experienced no problems, but since I obviously cannot be held accountable, advisable is to make a backup of your game directory first.

This version control works for installation of most mods, but requires the user to read the mod instructions carefully, be selective, resolve dependencies and prevent mentioned collisions. given:

## Requirements

User has knowledge of git and extended regular expressions, and at least a bit of bash and rsync.
Also some knowledge of Fallout 4 modules is convenient, and a general willingness to read their documentation.

Commandline tools are installed:
git, 7z, unrar, unzip, rsync, perl, sed, bash, sort, uniq, wc


## git

Revision control; branches, bisecting problems, cherry-picking commits and resolving conflicts; nuf said. These subjects won't be covered here, though. The user is supposed to be familiar with these concepts.


## rsync

Rsync has options for finetuning transfer and to prevent modules to have an effect outside the game directory.

## Initialize

clone the repository outside your Fallout 4 directory, where you do have sufficient space.
```Bash
git clone https://github.com/picobyte/mod4f4.git
cd mod4f4
chmod +x mod4f4
./mod4f4 --init
```

You nay want to also symlink your module download directory. This document assumes it is in `dnld/`. Also make the directory with the Fallout 4 .ini files a repository. The changes therein are listed here but not the revision control; manage that manually.

Adding the files under revision control takes a while. A `.gitignore` file in your f4/ directory excludes files from revision control, this is inadvisable; if modules do touch those files, you're sh*t out of luck.

## installing mods using mod4f4

### Usage

```
./mod4f4 [options..] <archive.7z|archive.rar|archive.zip> [rsync options..]

Options:

    --subset=<extended regular expression>  includes only the matching module (sub)directories
    --keep-mod: do not clean up the extracted module directory after commit.
    --no-stage: don't stage and commit (allows manual staging). This implies --keep-mod.
                Note that together with the --dry-run rsync option, no package contents are transferred.
                The filename and directoryname case-insensitivity resolution is still run, though.
    --message=<string> commit with this specified message.

for rsync options, see \`man rsync' or \`rsync --help'
```


### What mod4f4 does

1. The archive will be extracted; in a subdirectory with the basename, dependent on the contents of the archive.
2. Decided is where the data should be placed, based on the filenames in f4/ and extracted folder contents.
3. All data or whatever --subset matched will be rsynced to this location in Fallout 4.
4. case-insensitivity duplicates for directories and files will be resolved. Names adapt to the earlier tracked filenames.
5. Added and changed files are staged and committed.

The output is verbose, and not all warnings printed are severe. Just verify that the location of placement was correct.

### RTFM, and not only here

Always read the relevant documentation per mod. Modules have dependencies, or collisions, and order is important. This script does not resolve this. In below list I considered dependencies, and where unrestrained, based the order on the module's last update (or its first appearance).

As a last step, after the installation is done, run the Bodyslide steam executable you created and batch build, for all the models.

## Examples

```Bash
 ## F4SE
./mod4f4 --subset="f4se_0_06_21/(Data|.*\.(exe|dll))" dnld/f4se_*.7z

# actually the --subset=.. argument might be unneccesary (TODO: try without)
```

In Steam Library, right-click on Fallout 4. Properties. "Launch Options" add `f4se_loader.exe`

```Bash
## BodySlide [19-01-2021]

./mod4f4 --subset="(FOMod|Textures|Tools)" dnld/"BodySlide and Outfit Studio"*.7z
 
 # actually (again) the --subset=.. argument might be unneccesary (TODO: try without)
```

Create a `BodySlide` launcher in steam, using target `BodySlide.exe` and start in<br>
`"${HOME}/.steam/steam/steamapps/common/Fallout 4/Data/Tools/BodySlide"`<br>
Make sure to use proton experimental in the compatibility section.

```Bash
## Enhanced Vanilla Bodies [18-02-2017]

./mod4f4 dnld/"Nude Female-"*.7z
./mod4f4 dnld/"Nude Male Uncut-"*.7z


## Mod Configuration Menu [07-12-2019] prereq: F4SE

./mod4f4 "dnld/Mod Configuration Menu "*.zip


# Add required changes fin Fallout4Prefs.ini, e.g.

sed -i -r '/^\[Launcher\]/{
 N 
 s/(\r\n)(bEnableFileSelection=.+(.)$)?/\1\2bEnableFileSelection=1\r\n/
}' "$F4_ini_dir/Fallout4Prefs.ini"


## Looksmenu [04-11-2020] prereq: F4SE

./mod4f4 dnld/LooksMenu\ v*.7z


## CBBE [19-04-2020]

./mod4f4 --subset="((00|1[89]|2[0-6]) [A-Z][^/]+/|FOMod)" "dnld/Caliente's Beautiful Bodies Enhancer - v"*.7z


### dismemberment

./mod4f4 --subset="((00|1[89]|2[0-6]) [A-Z][^/]+/|FOMod)" "dnld/CBBE Reduced (with dismemberment)-"*.7z


## AWKCR [05-01-2020]

./mod4f4 "dnld/Armor and Weapon Keywords Community Resource v"*.rar


## AAF [17-01-2021] prereq: LooksMenu

./mod4f4 --subset="([0-9][^/]+/|FOMOD)" "dnld/AAF Beta"*.7z


# run the bodyslide batch build now.

```



