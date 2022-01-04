---
publish: true
relativeSavePath: /Library/Software
date created: "Tuesday, January 4th 2022, 10:45:45 am"
date modified: "Tuesday, January 4th 2022, 1:56:18 pm"
---

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
