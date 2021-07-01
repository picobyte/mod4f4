
# mod4f4
This script uses git to manage changes to Fallout 4

There are other management tools that are likely used more often. This script is to enable a Linux commandline, git-based manual packages manager.

clone the repository outside your Fallout 4 directory, where you do have sufficient space.
```Bash
git clone https://github.com/picobyte/mod4f4.git
cd mod4f4
chmod +x mod4f4
```

Symlink the directory where steam is installed, by default:
```Bash
ln -s "${HOME}/.steam/steam/SteamApps/common/Fallout 4" f4
```
Also put the Fallout 4 directory under revision control by git, add the original Falloput 4 with all required content as initial commit

```Bash
cd f4/
git init
```

Adding the files under revision control takes a while. Optionally create a `.gitignore` file containing files or locations that will never be changed, to limit the number of files under revision.
```Bash
git ls-files -o --exclude-standard | xargs -r -d "\n" -I {} git add "{}"
git commit -m 'clean Fallout 4'
cd -
```
You nay want to make a the directory with the .ini files a repository as well. The changes there are not managed by this mod4f4 script, however.

Always read the relevant documentation per mod. This script attempts to place files correctly in the root of Fallout 4 or its subdirectories as required, and maintains the original case of files and directories, to prevent duplicates. Finally it commits those changes. Always check that this was done correctly. 

If only part of the contents are required, then

You can run the script like this:

## F4SE
```Bash
./mod4f4 --include="f4se_0_06_21/(Data|.*\.(exe|dll))" */f4se_*.7z
```

In Steam Library, right-click on Fallout 4. Properties. "Launch Options" add `f4se_loader.exe`

```Bash
## BodySlide [19-01-2021]

./mod4f4 --include="(FOMod|Textures|Tools)" */"BodySlide and Outfit Studio"*.7z
```

Create a launcher in steam, using proton experimental pointing to BodySlide

```Bash
## Enhanced Vanilla Bodies [18-02-2017]

./mod4f4 */"Nude Female-"*.7z
./mod4f4 */"Nude Male Uncut-"*.7z


## Mod Configuration Menu [07-12-2019] prereq: F4SE

./mod4f4 *"/Mod Configuration Menu "*.zip


# Add required changes fin Fallout4Prefs.ini, e.g.

sed -i -r '/^\[Launcher\]/{
 N 
 s/(\r\n)(bEnableFileSelection=.+(.)$)?/\1\2bEnableFileSelection=1\r\n/
}' "$F4_ini_dir/Fallout4Prefs.ini"


## Looksmenu [04-11-2020] prereq: F4SE

./mod4f4 --stash=LooksMenu */LooksMenu\ v*.7z


## CBBE [19-04-2020]

./mod4f4 --include="((00|1[89]|2[0-6]) [A-Z][^/]+/|FOMod)" *"/Caliente's Beautiful Bodies Enhancer - v"*.7z


### dismemberment

./mod4f4 --include="((00|1[89]|2[0-6]) [A-Z][^/]+/|FOMod)" *"/CBBE Reduced (with dismemberment)-"*.7z


## AWKCR [05-01-2020]

./mod4f4 */"Armor and Weapon Keywords Community Resource v"*.rar


## AAF [17-01-2021] prereq: LooksMenu

./mod4f4 --include="([0-9][^/]+/|FOMOD)" */AAF\ Beta\ 161.1-31304-161-1b-1610914138.7z
```


