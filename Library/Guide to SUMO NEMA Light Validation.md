---
publish: true
relativeSavePath: /Library/Software
date created: "Monday, January 3rd 2022, 1:32:41 pm"
date modified: "Tuesday, January 4th 2022, 2:08:18 pm"
---

# Complete (opinionated) Guide to Manipulating, Building and Testing SUMO Core

## Clone and Build SUMO

How to clone, manipulate and build the SUMO source code

# SUMO Build + Contribution Instructions

---

## Overview

This document will walk through what I have found to be the best way to build [SUMO](SUMO.md) as well as contributing to the project. In general, the workflow looks like:

1. Fork [SUMO](https://github.com/eclipse/sumo)
   1. Max has already forked to [BittleResearchGroup](https://github.com/UnivOfAlabama-BittleResearchGroup/sumo?organization=UnivOfAlabama-BittleResearchGroup&organization=UnivOfAlabama-BittleResearchGroup)
1. Clone the [SUMO fork](https://github.com/UnivOfAlabama-BittleResearchGroup/sumo?organization=UnivOfAlabama-BittleResearchGroup&organization=UnivOfAlabama-BittleResearchGroup) to your local computer
   1. Good practice to always check the upstream branch ([SUMO](https://github.com/eclipse/sumo)) for commits that might impact what you are working on, as it is under active development
      1. If a change has been made, merge the upstream branch into your development branch and continue
1. Create a branch for your development & switch to that branch
1. Build SUMO in it’s entirety once
1. Development Loop
   1. Make changes to SUMO’s internals
   1. Build (just `sumo` or `sumo-gui`)
   1. Test
1. Ready for contribution?
   1. [Follow the instructions for contributing](https://sumo.dlr.de/docs/FAQ.html#how_do_code_contributions_work)
   1. Create a pull request on Github

## Detailed Instructions

### Cloning the Repository

Cloning the repository should be the same on all platforms. If you haven’t used Github since August of 2021, you may have to create a personal access token. This [StackOverflow](https://stackoverflow.com/a/68781050) answer walks you through the process concisely.

Then, assuming that you are using the [BittleResearchGroup](https://github.com/UnivOfAlabama-BittleResearchGroup/sumo?organization=UnivOfAlabama-BittleResearchGroup&organization=UnivOfAlabama-BittleResearchGroup) SUMO branch, you would clone it using

````
git clone https://github.com/UnivOfAlabama-BittleResearchGroup/sumo.git
````

or if you setup an SSH key:

````
git clone git@github.com:UnivOfAlabama-BittleResearchGroup/sumo.git
````

### Prepping the Repository up for Development

First, make sure that your `git config` is setup properly, so that you can contribute to SUMO if the time comes. See [instructions for contributing](https://sumo.dlr.de/docs/FAQ.html#how_do_code_contributions_work).

You should then create a branch to track your development

````
git branch max-dev
git switch max-dev
````

If you want to add the ability to track and merge SUMO’s main repository:

````
git remote add upstream https://github.com/eclipse/sumo.git
git fetch --all
````

Then, if you ever want to merge  [SUMO’s main branch](https://github.com/eclipse/sumo) into your development branch:

````
git fetch --all
git merge upstream/main
````

### Prepping VSCode for Development (on Windows)

I have tried both [VSCode](https://code.visualstudio.com/) and [CLion](https://www.jetbrains.com/clion/) and come to the conclusion that VSCode (with add-ons) is the better *IDE*.

#### Installing the Extensions & Necessary C++ Build Tools

The first step will be adding the necessary VSCode extensions, which the [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack) should cover.

I like to use package managers (homebrew on MacOS and apt-get on Linux), so I installed [Chocolatey](https://docs.chocolatey.org/en-us/) on Windows. You can install Chocolatey by following the [instructions here](https://docs.chocolatey.org/en-us/choco/setup).

Once [Chocolatey](https://docs.chocolatey.org/en-us/) is installed, you can install `cmake` with (**Make sure that you are inside of an administrative shell**)

````
choco install cmake
````

#### Installing Necessary C++ Libraries

SUMO provides a bundle of necessary C++ Libraries for Windows development. You can clone the libraries from [this repository](https://github.com/DLR-TS/SUMOLibraries/). You should clone the library in the `sumo` folder per their instructions.

````
git clone --recursive https://github.com/DLR-TS/SUMOLibraries
````

I found that I also need to set the `SUMO_LIBRARIES` environmental variable. If that is the case for you, I would suggest cloning the repository outside of `sumo` folder - as it will make `git` more complicated.

You will likely have to close and re-open VSCode if you set the environmental variables.

#### Configuring CMAKE

After installing the extensions and libraries. Open the `sumo` folder in VSCode. The following notification should pop up in the bottom right corner:



Select “Yes” to have CMAKE configure the project according to  `CMakeLists.txt`

Once the configuration finishes, the bottom bar should look something like below:



* The **CMake button** allows you to select the build variant.
  * I have not strayed from `Debug + FMI + Python + noJava`
* The **Active Kit Button** is for choosing the CMake Kit
  * On UNIX, this is clang or gcc
  * On Windows, this is Visual Studio Community 2019 Releases
    * You have to have the [Visual Compiler Tools](https://visualstudio.microsoft.com/downloads/) installed
* The bracketed **\[ALL_BUILD\]** button allows you to select the *program* to build.
  * This allows you to build **only** the executable that you want to debug.
  * Initially I would recommend building all the sources, and then switching to just **\[sumo-gui\]**  of **\[sumo\]** which saves compile time while debugging.

### Building SUMO

Its possible that your selected build target from above already built. If not, click on the ⚙️ Build button and CMake will start building the target.

* You should first build all of the sources and then change to just `[sumo]` or `[sumo-gui]`
* ==Reach out to Max if errors occur during the build process==

### Debugging SUMO

Debugging in VSCode is reliant on the `launch.json` file. To create it, go to the “Run and Debug” tab on the left sidebar and select **create a launch.json file**. The auto-configuration doesn’t matter as you are going to copy the file below.

The following should be a working example of a `launch.json` file that runs the NEMA test files from SUMO. It is platform specific, so if you are on something other than Window, reach out to Max.

````
{
 // Use IntelliSense to learn about possible attributes.
 // Hover to view descriptions of existing attributes.
 // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
 "version": "0.2.0",
 "configurations": [
 {
	 "name": "SUMO NEMA Debug",
	 "type": "cppvsdbg",
	 "request": "launch",
	 // from https://github.com/microsoft/vscode-cmake-tools/blob/main/docs/debug-launch.md
	 "program": "${command:cmake.launchTargetPath}",
	 // These args come from ${workspaceFolder}/tests/sumo/basic/tls/NEMA/custom_detector
	 "args": [
		 "--net-file=net.net.xml",
		 "--routes=input_routes.rou.xml",
		 "-a",
		 "input_additional.add.xml,input_additional2.add.xml",
		 "-t",
		 "--no-step-log"
 	],
	 "stopAtEntry": false,
	 "cwd": "${workspaceFolder}/tests/sumo/basic/tls/NEMA/custom_detector",
	 "environment": [],
	 "console": "externalTerminal",
 }
 ]
}
````

The launch task configured in `launch.json` does a couple of things automatically:

1. It builds the target specified in the bottom tool bar (**\[sumo-gui\]**)
1. It changes the directory of the subsequent task to what’s specified in `“cwd”`
1. It launches the built target with the arguments specified in `“args”`in an external terminal
1. It attaches a debugger to target

#### Attaching to an Existing SUMO Instance

If you are following along in [Single Intersection Airport Harper](Single%20Intersection%20Airport%20Harper.md), you were probably routed here.

To debug a “running” simulation, we need to attach to the process. The following addition to the `launch.json` will allow us to do just that

````
        {
            "name": "(cppvsdbg) Attach",
            "type": "cppvsdbg",
            "request": "attach",
            "program": "${workspaceRoot}/bin/sumo-guiD",
            "processId": "${command:pickProcess}",
        },
````

Then run the “(cppvsdbg) Attach” in the debug window and it should open a dialog box for you to search for the process ID. Just simply search “sumo” and you should find the process. This will attach the debugger and you can step through the simulation!

### Running a Simulation with the Debugger

Because this document was created to help extend *NEMA dual ring* controller, we will start by debugging its entry point: `src\microsim\traffic_lights\NEMAController.cpp`.

The bulk of the logic occurs inside the [NEMALogic](https://github.com/UnivOfAlabama-BittleResearchGroup/sumo/blob/99315ba0f2ed1f4a9411520633dbcab8bfeb8bcd/src/microsim/traffic_lights/NEMAController.cpp#L593) function.

You can place a break point on any line in this code and start debugging.

[The annotated PDF ](https://github.com/mschrader15/notes-public/blob/main/Library/Software/NEMA_flow_correct.pdf) provides a graphical view of the NEMALogic function.

**More will be added to this document as necessary…**

## Run SUMO with Econolite EOS

How to run [SUMO](SUMO.md) with Econolite controllers in parallel

# Running SUMO with Econolite EOS

*Note: This only works on Windows computers for the time being*

## Overview

This document covers the process of running a *software in loop* [SUMO](SUMO.md) simulation with Econolite EOS Virtual Controllers.

There are several steps:

1. Download the Econolite Controller
1. Install the [eos-api](https://github.com/UnivOfAlabama-BittleResearchGroup/eos-api)

## Download the Controllers

The EOS Virtual Controller must be downloaded from UA’s box. This [link](https://alabama.box.com/s/8pps2mv9c88dbi6qp1uk6nq0qb415epf) should work if you are signed in with a UA email. If it doesn’t, send an email to [me](mailto:mcschrader@crimson.ua.edu).

You should download the entire folder and extract it to a convenient location on your computer. The code will create a duplicate of the `bin` folder for each controller in the simulation, so it likely makes sense to stick the default controller install under a parent folder.

### Download the Configurations

The configuration files for the three intersections in the airport-harper network should have been included in the download in the `configurations` folder

I would keep the files in the same relative location and link to them in a simulation input file

## Install the EOS-API

The “unofficial” or reverse-engineered API is hosted on Github [here](https://github.com/UnivOfAlabama-BittleResearchGroup/eos-api). `pip` can be used to install it into the Python environment that you are using to run the simulation.

If you are simulating the [airport-harper network](https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo), then you can simply install eos-api via `pip install -r requirements.txt`. If you are not, call `pip install git+https://github.com/UnivOfAlabama-BittleResearchGroup/eos-api`

## Updating Paths

See [Single Intersection Airport Harper > Running A Simulation with EOS SIL](Single%20Intersection%20Airport%20Harper.md#running-a-simulation-with-eos-sil)

## Known Issues

* If a simulation fails before the actual SUMO simulation starts, this may be because the last simulation did not properly kill the EOS Controllers.
  * The fix is to kill all controllers and re-try the simulation
    * You can kill all controllers by entering `TASKKILL /F /IM eosapp.exe` in a command prompt

## Running Airport-Harper-Sumo

How to run a simplified Airport-Harper-Network

# How to Run Single Intersection Simulations in Airport-Harper Repo

## Overview

This page will cover running a single intersection simulation for validation purposes.

There are two “modes” that the simulation will have to run in:

1. Standalone using SUMO’s NEMA implementation
1. [Running SUMO with Econolite EOS](Running%20SUMO%20with%20Econolite%20EOS.md)

**You must have SUMO 1.11.0 installed to use NEMA, or be using the version that you built from source**

* To use the SUMO that you built from source, specify that version in the config .json file by using the `SUMO_EXE` parameter

## Background

The Airport Harper repository is flexible, but I have created a streamlined workflow that I am quite fond of. Here is how it works:

![Drawing 2022-01-04 10.48.22.excalidraw.png](Drawing%202022-01-04%2010.48.22.excalidraw.png)

## Running the Single Intersection Simulation

### Setting up [Airport-Harper-SUMO](https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo)

1. Clone https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo to a convenient place on your computer
   1. `git clone https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo.git`
1. Fetch all remote branches
   1. `git fetch --all`
1. Setup a virtual environment
   1. You may have already done this (skip below if so), but if not it is good practice and makes everything easier
   1. Inside of the `airport-harper-sumo` directory, run:
      1. `python3 -m venv venv`
   1. Activate the virtual environment (with Powershell)
      1. `venv\Scripts\Activate.ps1`
1. Install the requirements
   1. In a powershell with the virtual environment activated, run
      1. `pip install -r requirements.txt`
1. Switch to the `single-intersection` branch
   1. `git switch single-intersection`
1. Set the `AIRPORT_HAPER_OUTPUT` environmental variable
   1. This variable specifies where simulation outputs will be saved.

### Running a Simple Simulation

The SUMO files required to run a simple simulation live [here](https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo/blob/ba6078420445f254109c24f7cb3547854a470e76/sumo-xml/single-intersection)

A pre-configured configuration file exists [here](https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo/blob/3e983e08b614c0005807c3aa6ccc4e3e0754f01f/sim-settings/one-intersection/02_24_20-Simple.json#L1-L6)

Assuming that you are using VSCode, you should first create a `launch.json` file to run the simulation. Here is what mine looks like:

````
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Run Simulation",
            "type": "python",
            "request": "launch",
            "program": "${workspaceRoot}\\run.py",
            "console": "integratedTerminal",
            "args": [
                "--config_path",
                "${workspaceRoot}\\sim-settings\\one-intersection\\02_24_20-Simple.json"
            ]
        }
    ]
}
````

Running this debug configuration should successfully launch SUMO. If you want to debug a **SUMO build** using this configuration, you should first specify the path to the build in the configuration file like:

````
{
    "run_name": "Single-Intersection-NEMA",
    "SUMO_EXE": "C:\\Users\\gle\\Documents\\tmp\\sumo\\bin\\sumo-guiD.exe",
    "mode": "No Traci",
	 ....
}
````

Then follow the instructions in [SUMO Build Instructions > Attaching to an Existing SUMO Instance](SUMO%20Build%20Instructions.md#attaching-to-an-existing-sumo-instance)

### Running A Simulation with EOS SIL

To run the simulation with EOS in-the-loop, use the configuration file [here](https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo/blob/ba6078420445f254109c24f7cb3547854a470e76/sim-settings/one-intersection/02_24_20-EOS.json)

This file will require more setup, as some of the configurations are absolute paths to your file system. There is probably a better way to handle the EOS aspect, but I haven’t thought of one.

The settings that you will have to change are:

````
    "EOS_App_Path": "C:\\Users\\gle\\Documents\\EOS-Virtual-Controller\\bin\\eosapp.exe",
    "EOS_Cfg_Folder": "C:\\Users\\gle\\Documents\\EOS-Virtual-Controller\\configurations",
    "EOS_TESTING_ROOT": "C:\\Users\\gle\\Documents\\EOS-Virtual-Controller",
    "EOS_HIGH_RES_PATH": "C:\\Users\\gle\\Documents\\EOS-Virtual-Controller\\HighResTranslator\\HighResTranslator.exe", 
````

You should change them to the locations you used in [Running SUMO with Econolite EOS > Download the Controllers](Running%20SUMO%20with%20Econolite%20EOS.md#download-the-controllers)

## Manipulating the Simulation

According to the two configuration files referenced above, the simulation will run for 3600 seconds with the traffic defined by [simple.rou.xml](https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo/blob/ba6078420445f254109c24f7cb3547854a470e76/sumo-xml/single-intersection/routes/simple.rou.xml#L18-L19). If you want the simulation to run for shorter, change the `end-time` parameter in the configuration files. If you want the traffic to be heavy / less heavy, change the flow parameters in  [simple.rou.xml](https://github.com/UnivOfAlabama-BittleResearchGroup/airport-harper-sumo/blob/ba6078420445f254109c24f7cb3547854a470e76/sumo-xml/single-intersection/routes/simple.rou.xml#L18-L19)

If you have any other questions, ask me!

The next step will be [Post-Processing Traffic Light Behavior](Post-Processing%20Traffic%20Light%20Behavior.md)

## Analyzing the Light Behavior Using Jupyter Notebook

# Post-Processing Traffic Light Behavior

WIP
