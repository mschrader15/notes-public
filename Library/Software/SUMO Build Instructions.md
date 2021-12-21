---
publish: true
relativeSavePath: "/Library/Software"
---

# SUMO Build + Contribution Instructions
---
## Overview
This document will walk through what I have found to be the best way to build SUMO as well as contributing to the project. In general, the workflow looks like:

1. Fork [SUMO](https://github.com/eclipse/sumo)
	1. Max has already forked to [BittleResearchGroup](https://github.com/UnivOfAlabama-BittleResearchGroup/sumo?organization=UnivOfAlabama-BittleResearchGroup&organization=UnivOfAlabama-BittleResearchGroup)
2. Clone the [SUMO fork](https://github.com/UnivOfAlabama-BittleResearchGroup/sumo?organization=UnivOfAlabama-BittleResearchGroup&organization=UnivOfAlabama-BittleResearchGroup) to your local computer
	1. Good practice to always check the upstream branch ([SUMO](https://github.com/eclipse/sumo)) for commits that might impact what you are working on, as it is under active development
		1. If a change has been made, merge the upstream branch into your development branch and continue 
3. Create a branch for your development & switch to that branch
4. Build SUMO in it’s entirety once
5. Development Loop
	1. Make changes to SUMO’s internals
	2. Build (just `sumo` or `sumo-gui`)
	3. Test
6. Ready for contribution?
	1. [Follow the instructions for contributing](https://sumo.dlr.de/docs/FAQ.html#how_do_code_contributions_work)
	2. Create a pull request on Github

## Detailed Instructions
### Cloning the Repository
Cloning the repository should be the same on all platforms. If you haven’t used Github since August of 2021, you may have to create a personal access token. This [StackOverflow](https://stackoverflow.com/a/68781050) answer walks you through the process concisely. 

Then, assuming that you are using the [BittleResearchGroup](https://github.com/UnivOfAlabama-BittleResearchGroup/sumo?organization=UnivOfAlabama-BittleResearchGroup&organization=UnivOfAlabama-BittleResearchGroup) SUMO branch, you would clone it using 

```
git clone https://github.com/UnivOfAlabama-BittleResearchGroup/sumo.git
``` 

or if you setup an SSH key:

```
git clone git@github.com:UnivOfAlabama-BittleResearchGroup/sumo.git
``` 

### Prepping the Repository up for Development
First, make sure that your `git config` is setup properly, so that you can contribute to SUMO if the time comes. See [instructions for contributing](https://sumo.dlr.de/docs/FAQ.html#how_do_code_contributions_work).

You should then create a branch to track your development

```
git branch max-dev
git switch max-dev
```

If you want to add the ability to track and merge SUMO’s main repository:
```
git remote add upstream https://github.com/eclipse/sumo.git
git fetch --all
```

Then, if you ever want to merge  [SUMO’s main branch](https://github.com/eclipse/sumo) into your development branch:
```
git fetch --all
git merge upstream/main
```
### Prepping VSCode for Development (on Windows)
I have tried both [VSCode](https://code.visualstudio.com/) and [CLion](https://www.jetbrains.com/clion/) and come to the conclusion that VSCode (with add-ons) is the better IDE.

#### Installing the Extensions & Necessary C++ Build Tools
The first step will be adding the necessary VSCode extensions, which the [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack) should cover. 

I like to use package managers (homebrew on MacOS and apt-get on Linux), so I installed [Chocolatey](https://docs.chocolatey.org/en-us/) on Windows. You can install Chocolatey by following the [instructions here](https://docs.chocolatey.org/en-us/choco/setup). 

Once [Chocolatey](https://docs.chocolatey.org/en-us/) is installed, you can install `cmake` with (**Make sure that you are inside of an administrative shell**)
```
choco install cmake
```
#### Installing Necessary C++ Libraries
SUMO provides a bundle of necessary C++ Libraries for Windows development. You can clone the libraries from [this repository](https://github.com/DLR-TS/SUMOLibraries/). You should clone the library in the `sumo` folder per their instructions. 
```
git clone --recursive https://github.com/DLR-TS/SUMOLibraries
```

I found that I also need to set the `SUMO_LIBRARIES` environmental variable. If that is the case for you, I would suggest cloning the repository outside of `sumo` folder - as it will make `git` more complicated. 

You will likely have to close and re-open VSCode if you set the environmental variables.
 
 #### Configuring CMAKE

After installing the extensions and libraries. Open the `sumo` folder in VSCode. The following notification should pop up in the bottom right corner:

![Pasted image 20211220142923.png](/resources/Pasted_image_20211220142923.png)

Select “Yes” to have CMAKE configure the project according to  `CMakeLists.txt`

Once the configuration finishes, the bottom bar should look something like below:

![vscode_footer.png](/resources/vscode_footer.png)

- The **CMake button** allows you to select the build variant.
	- I have not strayed from `Debug + FMI + Python + noJava`
- The **Active Kit Button** is for choosing the CMake Kit
	- On UNIX, this is clang or gcc
	- On Windows, this is Visual Studio Community 2019 Releases
		- You have to have the [Visual Compiler Tools](https://visualstudio.microsoft.com/downloads/) installed
- The bracketed **[ALL_BUILD]** button allows you to select the *program* to build. 
	- This allows you to build **only** the executable that you want to debug.
	- Initially I would recommend building all the sources, and then switching to just **[sumo-gui]**  of **[sumo]** which saves compile time while debugging.
### Building SUMO
Its possible that your selected build target from above already built. If not, click on the ⚙️ Build button and CMake will start building the target.
- You should first build all of the sources and then change to just `[sumo]` or `[sumo-gui]`
- ==Reach out to Max if errors occur during the build process==
### Debugging SUMO
Debugging in VSCode is reliant on the `launch.json` file. To create it, go to the “Run and Debug” tab on the left sidebar and select **create a launch.json file**. The auto-configuration doesn’t matter as you are going to copy the file below.

The following should be a working example of a `launch.json` file that runs the NEMA test files from SUMO. It is platform specific, so if you are on something other than Window, reach out to Max. 

```
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
```

The launch task configured in `launch.json` does a couple of things automatically:
1. It builds the target specified in the bottom tool bar (**[sumo-gui]**)
2. It changes the directory of the subsequent task to what’s specified in `“cwd”`
3. It launches the built target with the arguments specified in `“args”`in an external terminal
4. It attaches a debugger to target
###  Running a Simulation with the Debugger
Because this document was created to help extend NEMA dual ring controller, we will start by debugging its entry point: `src\microsim\traffic_lights\NEMAController.cpp`.

The bulk of the logic occurs inside the [NEMALogic](https://github.com/UnivOfAlabama-BittleResearchGroup/sumo/blob/99315ba0f2ed1f4a9411520633dbcab8bfeb8bcd/src/microsim/traffic_lights/NEMAController.cpp#L593) function.

You can place a break point on any line in this code and start debugging.

[The annotated PDF ](https://github.com/mschrader15/notes-public/blob/main/Library/Software/NEMA_flow_correct.pdf) provides a graphical view of the NEMALogic function.

**More will be added to this document as necessary…**

