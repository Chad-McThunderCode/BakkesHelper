# BakkesHelper

Bash script to handle installing, updating and running BakkesMod whenever Rocket League is started.

## Usage

Download this script and add it to Rocket League's Launch Options in Steam:
    "/path/to/BakkesHelper"; %COMMAND%

You can save this script anywhere, you can clone the repository, it's up to you. I encourage to clone the repository and to add a symlink in your /usr/bin, that way it's available in the PATH, which cleans up the Launch Options:
    "BakkesHelper"; %COMMAND%

Note: The semicolon ensures that Rocket League isn't launched until BakkesHelper has checked and, if necessary, installed BakkesMod. It's not strictly necessary.

If BakkesMod is not installed already, or if it has to be updated, the user has to click through the setup. **Untick 'Run Bakkesmod' on the last page**. If you don't, Rocket League will not run until you close BakkesMod.

#### BakkesMod will always warn you about an unsupported version of Rocket League, inject anyways.
#### BakkesMod will always error out telling you the injection failed, even when it doesn't - it will work.
#### Make sure to only inject it once your game has fully loaded, injecting during the loading screen might crash it - BakkesHelper has a builtin delay of 20 seconds, which might not be enough on slower systems.

## Dependencies

- wget
- protontricks
- unzip
- git

### Why are these dependencies necessary?

We use **protontricks** to run BakkesMod on Rocket League's Proton prefix.
We use **wget** and **unzip** to download and unpack BakkesMod's latest release from the official repository: https://github.com/bakkesmodorg/BakkesModInjectorCpp/releases
We use **git** to check the latest commit hash of the official repository to make sure BakkesMod is up to date, or to reinstall it. It would be preferable to rely on BakkesMod's internal updating procedure, but we did not want to meddle with neither BakkesMod nor RocketLeague.

## What BakkesHelper does

- It runs **my** code on **your** computer. Always be aware of that and check the source code. I've tried to make it self-explanatory, it doesn't require elevated privileges, it doesn't require extraordinary dependencies, but it's still code from the internet.
- It creates a config directory at ~/.config/bakkes to store the installed BakkesMod version, the version is the latest commit hash.
- It creates a temporary working directory in /tmp on every launch
- It checks for the required dependencies and may error out, telling the user to install what's missing
- It uses **protontricks** to see if Rocket League is installed
- It uses **protontricks** to run a small script inside the Proton prefix to extract the WINEPREFIX directory
- It checks if BakkesMod is installed, then compares the installed version to the latest version using **git**
- If BakkesMod is not installed or if the version doesn't match the latest, it will download BakkesMod using **wget**, unpack it into the temporary working directory using **unzip**, and then run it on the Proton prefix - here, the user is supposed to follow the install instructions
- Once the installation is complete, and on every launch after that where installing isn't necessary, BakkesHelper will wait for Rocket League to be launched, then launch BakkesMod and wait for Rocket League to be closed, then close BakkesMod and exit.

## What BakkesHelper does not
- It **does not support Epic Games**, sorry. It *might* in the future, but not for now.
- It does not modify BakkesMod or Rocket League and hence,
- it does not automate the setup itself, nor does it use BakkesMod's internal updating procedure
- It does not run on startup, only when Rocket League is launched (though it might crash and stay open, my bad?)

## Troubleshooting
- BakkesHelper logs to Steam's stderr, so if you need to troubleshoot, close Steam, run it from a terminal and keep the terminal open
- If the install gets messed up, you can always delete the file ~/.config/bakkes/version, the version check will then fail and BakkesHelper will download and run the setup on the next launch
- /path/to/BakkesHelper is not a real path, replace it with the actual absolute path to it in the Launch Options, just to be clear
- If Rocket League crashes, try injecting at a later point after launching
- If you want to remove BakkesHelper, remember to remove it from the Launch Options
- If you encounter dependency errors, use your system's Package Manager to install them:

    pacman -S wget protontricks unzip git #Arch-based distros

    apt-get install wget protontricks unzip git #Ubuntu-based distros

## Bugs
- Probably many. Nothing that should mess up your system, because no system files are targeted. Report them to me.
- Rocket League will launch even after the setup is cancelled or BakkesHelper crashes, that's intended behavior even if it feels odd. You can still play without BakkesMod, after all.
- I haven't yet investigated how BakkesMod stores its plugins. I expect all plugins to be destroyed when it BakkesHelper reinstalls it, though the BakkesMod install directory is never actually deleted, it's passed - as it exists - to the BakkesMod setup. I'll get to it, eventually. It would probably have taken less time than to write this README, but, as the Germans say, **"Einen Tod muss man sterben."**
