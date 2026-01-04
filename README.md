# BakkesHelper

Bash script to handle installing, updating and running BakkesMod whenever Rocket League is started.

## Usage

Download this script and add it to Rocket League's Launch Options in Steam:

    "/path/to/BakkesHelper"; %COMMAND%

You can save this script anywhere, you can clone the repository, it's up to you. I encourage to clone the repository and to add a symlink in your /usr/bin, that way it's available in the PATH, which cleans up the Launch Options:

    "BakkesHelper"; %COMMAND%

To clone this repository, navigate to the directory you want to clone it to, then run:

    git clone https://github.com/Chad-McThunderCode/BakkesHelper

To make a symlink in /usr/bin to BakkesHelper, enter the BakkesHelper directory, then run:

    ln -s "$(pwd)/BakkesHelper" "/usr/bin/BakkesHelper"

*Note that creating files in your /usr/bin directory requires root privileges, but BakkesHelper does not.*

### Additional usage information
The semicolon ensures that Rocket League isn't launched until BakkesHelper has checked and, if necessary, installed BakkesMod. It's not strictly necessary, but encouraged.

If BakkesMod is not installed already, or if it has to be updated, the user has to click through the setup. **Untick 'Run Bakkesmod' on the last page**. If you don't, Rocket League will not run until you close BakkesMod.

Make sure to only inject it once your game has fully loaded, injecting during the loading screen might crash it - BakkesHelper has a builtin delay of 20 seconds, which might not be enough on slower systems. Or it may annoy people on faster systems. BakkesHelper checks the INJECT_DELAY environment variable which you can add - *outside the quotes* - to the Rocket League Launch Options:

    INJECT_DELAY=60 "BakkesHelper"; %COMMAND%
    
BakkesMod may warn you about an unsupported version of Rocket League, inject anyways.
BakkesMod may error out telling you the injection failed, even when injection succeeds.
Both issues above seem to be fixed in the current version, leaving notes as not to confuse users when they reappear.

## Dependencies

- wget
- unzip
- git
#### protontricks **was removed as a dependency** - while it's a cleaner solution to the problem, it caused issues itself and it's not in the official repos, so inexperienced users would have to jump through hoops - something BakkesHelper is specifically trying to prevent.
### Why are these dependencies necessary?

We use **wget** and **unzip** to download and unpack BakkesMod's latest release from the official repository: https://github.com/bakkesmodorg/BakkesModInjectorCpp/releases.

We use **git** to check the latest commit hash of the official repository to make sure BakkesMod is up to date, or to reinstall it. It would be preferable to rely on BakkesMod's internal updating procedure, but we did not want to meddle with neither BakkesMod nor RocketLeague.

## What BakkesHelper does

- It runs **my** code on **your** computer. Always be aware of that and check the source code. I've tried to make it self-explanatory, it doesn't require elevated privileges, it doesn't require extraordinary dependencies, but it's still code from the internet.
- It creates a config directory at ~/.config/bakkes to store the installed BakkesMod version, the version is the latest commit hash
- It creates a temporary working directory in /tmp on every launch
- It checks for the required dependencies and may error out, telling the user to install what's missing
- It extracts compatibility tools (i.e. Proton) and Rocket League's prefix directory from the environment
- It checks if BakkesMod is installed, then compares the installed version to the latest version using **git**
- If BakkesMod is not installed or if the version doesn't match the latest, it will download BakkesMod using **wget**, unpack it into the temporary working directory using **unzip**, and then run it on the Proton prefix - here, the user is supposed to follow the install instructions
- Once the installation is complete, and on every launch after that where installing isn't necessary, BakkesHelper will wait for Rocket League to be launched, then launch BakkesMod and wait for Rocket League to be closed, then close BakkesMod and exit - that's necessary, because Steam would consider Rocket League to be running for as long as BakkesMod would run, counting towards your playtime and preventing other games from being launched

## What BakkesHelper does not
- It **does not support Epic Games**, sorry. It *might* in the future, but not for now.
- It does not modify BakkesMod or Rocket League and hence,
- it does not automate the setup itself, nor does it use BakkesMod's internal updating procedure
- It does not run on startup, only when Rocket League is launched (though it might crash and stay open, my bad?)

## Troubleshooting
- BakkesHelper logs to Steam's stderr, so if you need to troubleshoot, close Steam, run it from a terminal and keep the terminal open, messages are prefixed with **BakkesHelper:**
- If the install gets messed up, you can always delete the file ~/.config/bakkes/version, the version check will then fail and BakkesHelper will download and run the setup on the next launch
- /path/to/BakkesHelper is not a real path, replace it with the actual absolute path to it in the Launch Options, just to be clear - or create the symlink as outlined above
- If Rocket League crashes, try injecting at a later point after launching
- If BakkesHelper malfunctions, chances are the BakkesHelperDaemon.sh process is still active - use pkill -e BakkesHelperDae, htop or any other method to remove it
- If you want to remove BakkesHelper, remember to remove it from the Launch Options as well
- If you encounter dependency errors, use your system's Package Manager to install them:
  
  Arch-Linux:
  
        pacman -S wget unzip git #Arch-based distros
  Ubuntu:
  
        apt-get install wget unzip git #Ubuntu-based distros

## Unintended ways to use it
It will error out if you run it standalone, not through steam, i.e. from a terminal. If you still want to do it for some reason and want it to work, you need to launch it with two environment variables set:

    STEAM_COMPAT_TOOL_PATHS=/path/to/proton-directory STEAM_COMPAT_DATA_PATH=/path/to/compatdata/252950

Technically speaking, you can run any trainer/executable on any game in any proton prefix with minor modifications to the bash script. **If you're able to do this, you're able to build and install protontricks and use protontricks-launch, do that.**

## Bugs
- Probably many. Nothing that should mess up your system, because no system files are targeted. Report them to me.
- Rocket League will launch even after the setup is cancelled or BakkesHelper crashes, that's intended behavior even if it feels odd. You can still play without BakkesMod, after all.
- I haven't yet investigated how BakkesMod stores its plugins. I expect all plugins to be destroyed when it BakkesHelper reinstalls it, though the BakkesMod install directory is never actually deleted, it's passed - as it exists - to the BakkesMod setup. I'll get to it, eventually. It would probably have taken less time than to write this README, but, as the Germans say, **"Einen Tod muss man sterben."**

## I don't trust you, can't I do this manually? -Of course!

### With protontricks - harder to set up, easier to do
You can download the latest release of BakkesMod from the official website/repository, unpack it and use protontricks yourself to first install and later run BakkesMod. All you need is to install protontricks (and anything to unpack a *zip file, which most desktop environments and/or file browsers already ship with anyways). If you have access to the AUR, you can install protontricks through any AUR helper, if not, you'll need to build it yourself following their instructions.

Run the installer first, it will default to installing in the correct prefix under *drive_c/Program Files/BakkesMod*:

    protontricks-launch --appid 252950 /path/to/BakkesModSetup.exe
    
Now, run the same command again, except targeting the installed BakkesMod.exe instead of the setup:

    protontricks-launch --appid 252950 /path/to/pfx/drive_c/Program\ Files/BakkesMod/BakkesMod.exe
    
Launch Rocket League, wait for it to have loaded, then run the above command and inject it.

### **Without** protontricks - no further setup, but harder to do

You need to figure out which proton version is used to launch Rocket League and where it lives. Also which prefix it is run in. To do that, you can extract the env from within the Launch Options:

    env >&2 # %COMMAND%

This will prevent Rocket League from launching, but output the env to Steam's stderr, to see it, open steam using the terminal, then look for the two environment variables mentioned under the https://github.com/Chad-McThunderCode/BakkesHelper/edit/main/README.md#unintended-ways-to-use-it section.
Use that to modify the following command:

    WINEPREFIX="/path/to/252950/pfx" "/path/to/proton-directory/files/bin/wine" "/path/to/BakkesMod.exe"

You set the prefix, you run the wine binary shipped with the proton version in question and you pass it the BakkesMod binary. This is exactly what BakkesHelper does for you, minus the installation and keeping watch on BakkesMod to close it when Rocket League closes.
