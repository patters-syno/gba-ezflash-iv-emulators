# EZ-Flash IV Exit-Patched Emulator Collection
This is a collection of emulators for the Gameboy Advance, SRAM- and exit-patched to function optimally with the EZ-Flash IV flashcart:

Emulator|Target System|Author(s)|Released
:-------|:------------|:--------|:---
[PocketNES 1-4-2020](https://github.com/Dwedit/PocketNES/releases)|Nintendo NES|Loopy, later FluBBa, Dwedit|Jan 2001?
[PCEAdvance 7.5](https://web.archive.org/web/20150430211123/http://www.ndsretro.com/gbadown.html)|NEC PC Engine / PC Engine CD-ROM² |FluBBa|Apr 2003
[Goomba Paletted 2.40](http://goomba.webpersona.com)|Nintendo Gameboy|FluBBa|Oct 2003
[SNESAdvance 0.1f](https://web.archive.org/web/20080208234615/http://www.snesadvance.org/index.html)|Nintendo SNES|Loopy, FluBBa|Feb 2005
[SMSAdvance 2.5](https://web.archive.org/web/20150430211123/http://www.ndsretro.com/gbadown.html)|SEGA Master System / Game Gear / SG-1000|FluBBa|Jul 2005
[Cologne 0.8](https://web.archive.org/web/20150430211123/http://www.ndsretro.com/gbadown.html)|ColecoVision|FluBBa|Jan 2006
[Goomba Color 2019_5_4](https://www.dwedit.org/gba/goombacolor.php)|a Goomba fork to add Gameboy Color|Dwedit|Jan 2006
[MSXAdvance 0.2](https://web.archive.org/web/20150430211123/http://www.ndsretro.com/gbadown.html)|MSX-1 (*version 0.2 is most compatible*)|FluBBa|Mar 2006
[NGPAdvance 0.3](https://web.archive.org/web/20150430211123/http://www.ndsretro.com/gbadown.html)|SNK Neo Geo Pocket / NGP Color|Flubba|Jul 2008
[Jagoomba 0.4a](https://github.com/EvilJagaGenius/jagoombacolor/releases)|enhanced Goomba Color fork|Jaga|Nov 2021

## Dependencies
Python 3

## Background
These emulators were originally designed to be used in a number of ways: 
1. assemble many game ROMs into a large ```.gba``` compilation and browse the games from a menu upon launch  
   - *slow to load into EZ-Flash PSRAM before execution, single 64KB SRAM save may constrain*  
2. as plugins for the Pogoshell file manager  
   - *only supports much older flashcart devices e.g. Flash2Advance*  
3. bundle each game ROM with its own copy of the emulator into a standalone ```.gba``` file  
   - *best for EZ-Flash IV - plenty of SD card storage, quick to load*  

## Purpose
This collection serves use case 3 above. In each emulator folder the build script will iterate through the ROMs in the current folder building a ```.gba``` executable for each title. They invoke my own Python 3 [gba-emu-compilation-builders](https://github.com/patters-syno/gba-emu-compilation-builders) scripts, which may also serve use case 1 if required.

Each emulator's Exit function in the L+R menu was typically intended for Pogoshell-era flashcarts. Where needed, this function has been manually patched so that Exit returns to the EZ-Flash IV menu.

## Usage
- Install Python 3 if not present
- Add games to the emulator folder
- See notes on firmware versions below
- Run **build.bat** from a Command Prompt (Windows), or **build.sh** from a Terminal session (macOS / Linux)
- Copy the resulting ```.gba``` files to EZ-Flash IV SD card

## EZ-Flash IV Versions
#### Firmware 2.x
- The build scripts will generate the required patch files to force 64KB SRAM saves for each executable, to be placed in the PATCH folder on the SD card.
- It is recommended that you disable the firmware's integrated GSS patcher (Global Soft-reset and Sleep). Add the following exclusions to the bottom of **KEYSET.CFG** at the root of your SD card:
  ```
  #GAMELIST TO SKIP GSS AUTOMATICALLY
  #EMULATORS
  COLG = 1   #Cologne
  GMBC = 1   #Goomba Color/Jagoomba
  GMBA = 1   #Goomba
  MSXA = 1   #MSXAdvance
  NGPA = 1   #NGPAdvance
  PCEA = 1   #PCEAdvance
  PNES = 1   #PocketNES
  SMSA = 1   #SMSAdvance
  SNAV = 1   #SNESAdvance
  ```
#### Firmware 1.x
- The ```.gba``` files produced by this collection are ready to be copied directly onto the SD card, do not use the EZ4 Client to patch them.
- The emulators in this collection have all been header-patched to force 64KB SRAM saves, using cory1492's v2 patcher (EZ4-64-2). The 1.x firmware reads some metadata from the GBA ROM header to determine save size, and without this fix many homebrew binaries will default to 32KB.
- gbata7 was used to fix the GBA ROM header after these patches (emulators crash on some firmwares without this fix).
- You will need to edit **build.bat** (for Windows) and **build.sh** (for macOS and Linux) to change the compile script options from ```-pat``` to ```-sav``` so that the blank save files are generated for each executable, to be placed in the Saver folder on the SD card.

## Emulator Tips
#### Cologne
- find the BIOS rom with the no-delay patch to speed up the boot time: "ColecoVision BIOS (1982) (No Title Delay Hack)"
- R+Start to bring up the virtual controller keypad
#### MSXAdvance
- the BIOS you need is "MSX System v1.0 + MSX BASIC (1983)(Microsoft)[MSX.ROM]"
- R+Start to bring up the virtual keyboard
- the emulator does not auto-select the correct mapper. You often need to change this (typically to Konami5) in Other Settings and restart the emulator for it to take effect
- [compatibility list](https://web.archive.org/web/20070612060046/http://boards.pocketheaven.com/viewtopic.php?t=3768)
- versions 0.3 and 0.4 [significantly broke compatibility](https://gbatemp.net/threads/msxadvance-compatibility-many-games-in-gamelist-txt-dont-work.609615/)
#### PCEAdvance
- audio tends to work pretty well in mixer mode, but you do need to restart the emulator after enabling it
- [CD-ROM ISO extracting guide](https://gbatemp.net/threads/pceadvance-cd-rom-support-howto-required.610542/)
- [CD-ROM / Super CD-ROM titles list](https://gamicus.fandom.com/wiki/List_of_PC-Engine_CD-ROM²_video_games)
#### SMSAdvance
- BIOS booting (effectively a blank 16KB ROM image) requires the system type to be hard set to Master System, assuming Master System BIOS games, because without a ROM the emulator cannot guess which system BIOS (SMS or GG) should be loaded
- "Lock toprows" is an option for Full Screen display mode useful for certain Master System games, such as Outrun, which can keeps the score/speedometer on screen despite cropping the image to the GBA resolution
#### SNESAdvance
- Start+Select+A+B for the emulator menu
- Select+Up/Down to change screen offset
- [list of best functioning games](https://web.archive.org/web/20050305113636/http://ygodm.tonsite.biz/snesadv/snesadv_gamelist.html)

## Exit-Patching Method
Each of the emulators uses some variation of the *visoly.s* source file. Since they're not identical, it's not trivial to make a binary patcher. A sample *visoly.s* is included for those adventurous enough to disassemble one of the emulators and to figure out the location of the *init_flashcart* function. This is where one of the two versions of the ez4_exit code is patched at.
