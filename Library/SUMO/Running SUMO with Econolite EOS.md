---
publish: true
relativeSavePath: /Library/Software
date created: "Monday, January 3rd 2022, 1:47:58 pm"
date modified: "Tuesday, January 4th 2022, 1:43:44 pm"
---

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

See *Single Intersection Airport Harper > Running A Simulation with EOS SIL*

## Known Issues

* If a simulation fails before the actual SUMO simulation starts, this may be because the last simulation did not properly kill the EOS Controllers.
  * The fix is to kill all controllers and re-try the simulation
    * You can kill all controllers by entering `TASKKILL /F /IM eosapp.exe` in a command prompt
