# Setup WTSN on Raspberry PI compute module 4

# Hardware MYIR FZ3
https://www.myirtech.com/list.asp?id=630
 
# Introduction
This repo contains vivado design for the wireless probe based on an ZymqMP Ultrascale+ (ZU3EG -  MYIR FZ3 )
 
# Getting Started
Prerequisites
Currently only the Xilinx Vivado 2020.2 suite is supported. You can install the WebPACK version (free), because all the boards used in this project have small devices that are supported in this free version of the Xilinx tools. The user is responsible for setting his environment up. The scripts call "vivado" and expect to get the 2020.2 version.
For this to work, run the bash (Linux) or batch (Windows) script <Vivado_installation_path>/Vivado/2020.1/settings64-Vivado.[bat,sh]
 
# What's in the folders?
- The library folder contains Vivado projects for modules, i.e. reusable blocks of logic that don't function on their own, i.e. projects that don't get flashed to a board. These are exported as IP so they can be imported in board projects.
 
- The projects folder contains board projects. These are baked into a bitfile and that bitfile gets included in a hardware platform file (XSA) for further use in the software projects.
 
- The software folder contains scripts to generate all neccessary files for buildroot! e.g. dts, dtb,...
 
# Workflow rules
The user has to adhere to the following rules, in order for him/her to successfully use this workflow:
 
Use _make_ to build the project.
Open the Vivado GUI and change the project as you want.
Place new source files in the "src" folder, then import them into the Vivado project.
Before you close Vivado, ALWAYS export project Tcl and save under the name "project_setup_<project_name>.tcl" in the project folder, overwriting the old file.
 
Use _git_ to add your new source files to the local repo.
git commit. Always describe the changes in a message, e.g. in the command line use the -m flag.
git push
 
# REPO:
```console
        git clone git@code.siemens.com:christoph.hohl/me_pre_myir.git
```
 
###############################################################################
# Workflow
**How to build a project**
The build process is make-script based, i.e. if you want to build a certain project (board or module) you go to that folder and run make:
```console
        cd projects/zu3cg
        make setup
```
-> setup the vivado project
OR
```console
        cd projects/zu3cg
        make build
```
-> setup the vivado project, build the .xsa and export it directly!
 
You can clean the output products of a project by running make clean. For board projects you can also clean the output products of the dependencies by running make clean-all:
```console
        make clean
```
OR
```console
        make clean-all
```
 
This will clean the output products of the board project and the output products of the dependencies.
 
Export the hardware platform file to the software project.
Currently there exists no automatic mechanism that transfers the results of the build process, i.e. the XSA-file, to the software project. This step has to be done manually.
 
 
# Create Hardware-files <a name = "buildroot-file"></a>
 
1. Export XSA-file (include Bitstream) "/project/hw/"
2. rename your XSA-file to system.xsa
 
 
 
###############################################################################
# Generate necessary Hardware-files
1. Modify **PATH** in "/me_pre_xx/software_ZyqnMP+/sw_init.sh"
 
```console
    path_xsa="../../project/fz3_zu3eg/hw"
```
2. generate Hardware-files
 
```console
    make clean
    make setup
    make build
```
 If not vivado found do:
```console
    source /tools/Xilinx/Vitis/2020.2/settings64.sh
```
 
#DEBUG -Device-Tree
-> add / modify system.dts in "output folder"
 
1. Add at "mmc@" -> SD-Card error  
            no-1-8-v;
 
2. modify
    chosen {
        bootargs = "earlycon console=ttyPS0,115200 clk_ignore_unused cpuidle.off=1 root=/dev/mmcblk0p2 rootfstype=ext4 rw rootwait";
        stdout-path = "serial1:115200n8";
    };
    aliases {
        mmc0 = "/amba/mmc@ff170000";
        mmc1 = "/amba/mmc@ff160000";
    };
   
##only for loopback
    ethernet@ff0b0000 {
        xlnx,ptp-enet-clock = <0x00>;
        phy-mode = "moca";
        fixed-link
                { speed = <1000>;
                  full-duplex;
                };
    };
 
3. compile system.dts to system.dtb
 
    Generate system.dts to system.dtb
    ```console
        dtc -I dts -O dtb -o system.dtb system.dts
    ```
    Generate system.dtb to  system.dts
    ```consol
        dtc -I dtb -O dts -o system.dts system.dtb
    ```
 
# Vivado Problems
 
## Clock Wizzard Problems
```console
    LANG=en_US.UTF-8
    LANGUAGE=en_US:en
    LC_CTYPE="en_US.UTF-8"
    LC_NUMERIC=en_US.UTF-8
    LC_TIME=en_US.UTF-8
    LC_COLLATE="en_US.UTF-8"
    LC_MONETARY=en_US.UTF-8
    LC_MESSAGES="en_US.UTF-8"
    LC_PAPER=en_US.UTF-8
    LC_NAME=en_US.UTF-8
    LC_ADDRESS=en_US.UTF-8
    LC_TELEPHONE=en_US.UTF-8
    LC_MEASUREMENT=en_US.UTF-8
    LC_IDENTIFICATION=en_US.UTF-8
    LC_ALL=
```
