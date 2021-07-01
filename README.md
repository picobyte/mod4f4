# mod4f4
Fallout 4 git based mod manager

git clone this repository outside your Fallout 4 directory, where you do have sufficient space, e.g. in ${HOME}/Fallout4_mods

Symlik the directory where steam is installed, this is by default:

ln -s "${HOME}/.steam/steam/SteamApps/common/Fallout 4" f4

# This script uses git to manage changes to Fallout 4
cd f4/
git init

# run this in the background, it takes a while
find . -type f -exec git add "{}" \+
git commit -m  'clean Fallout 4'&

cd -

# you nay want to mak a the directory with the .ini files ar repository as well. Those changes are not managed by the mod4f4 script, however.

chmod +x mod4f4

You can run the script like this:

### F4SE
./mod4f4 --include="f4se_0_06_21/(Data|.*\.(exe|dll))" */f4se_*.7z

#In Steam Library, right-click on Fallout 4. Properties. "Launch Options":
# f4se_loader.exe 


### BodySlide [19-01-2021]
./mod4f4 --include="(FOMod|Textures|Tools)" */"BodySlide and Outfit Studio"*.7z

# create a launcher in steam, using proton experimental pointing to BodySlide

### Enhanced Vanilla Bodies [18-02-2017]
./mod4f4 */"Nude Female-"*.7z
./mod4f4 */"Nude Male Uncut-"*.7z

### Mod Configuration Menu [07-12-2019] prereq: F4SE
./mod4f4 *"/Mod Configuration Menu "*.zip


sed -i -r '/^\[Launcher\]/{
 N 
 s/(\r\n)(bEnableFileSelection=.+(.)$)?/\1\2bEnableFileSelection=1\r\n/
}' "$F4_ini_dir/Fallout4Prefs.ini"

#### Looksmenu [04-11-2020] prereq: F4SE
./mod4f4 --stash=LooksMenu */LooksMenu\ v*.7z

#### CBBE [19-04-2020]
./mod4f4 --include="((00|1[89]|2[0-6]) [A-Z][^/]+/|FOMod)" *"/Caliente's Beautiful Bodies Enhancer - v"*.7z

#### dismemberment
./mod4f4 --include="((00|1[89]|2[0-6]) [A-Z][^/]+/|FOMod)" *"/CBBE Reduced (with dismemberment)-"*.7z

#### AWKCR [05-01-2020]
./mod4f4 */"Armor and Weapon Keywords Community Resource v"*.rar

#### AAF [17-01-2021] prereq: LooksMenu
./mod4f4 --include="([0-9][^/]+/|FOMOD)" */AAF\ Beta\ 161.1-31304-161-1b-1610914138.7z



