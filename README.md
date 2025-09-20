# RISC-V Reference SoC Tapeout Program VSD

## Setup Guide for Required Tools

#### <ins>Complete installation steps for all necessary tools are provided below:</ins>

### System Prerequisites
- Minimum 6 GB of RAM
- At least 50 GB of free disk space
- Ubuntu version 20.04 or newer
- 4 virtual CPU cores

### Adjusting Ubuntu VM Display Resolution
```bash
sudo apt update
sudo apt install build-essential dkms linux-headers-$(uname -r)
cd /media/spatha/VBox_GAs_7.1.8/
./autorun.sh
```


### **TOOL CHECK**

#### <ins>**Yosys**</ins>
### Installing Yosys

### Yosys is a framework for synthesis, and it’s essential for this RISC-V SoC project. Below are the steps to install Yosys.
```bash
$ sudo apt-get update
$ git clone https://github.com/YosysHQ/yosys.git
$ cd yosys
$ sudo apt install make               # If make is not installed
$ sudo apt-get install build-essential clang bison flex \
    libreadline-dev gawk tcl-dev libffi-dev git \
    graphviz xdot pkg-config python3 libboost-system-dev \
    libboost-python-dev libboost-filesystem-dev zlib1g-dev
$ make config-gcc
# Yosys build depends on a Git submodule called abc, which hasn't been initialized yet. You need to run the following command before running make
$ git submodule update --init --recursive
$ make 
$ sudo make install
```
### First, you fetch the Yosys source code from GitHub using git clone.
### Then, install dependencies that Yosys requires for building, such as clang, bison, flex, and others.
### After setting up the dependencies, you build the project using make and finalize the installation with sudo make install.
### The git submodule update ensures that all necessary submodules (in this case, abc) are initialized for a successful build.

<img width="995" height="425" alt="image" src="https://github.com/user-attachments/assets/78d0d9b6-3193-48f6-86f1-bc3cec5e7205" />

#### <ins>**Iverilog**</ins>
### Installing Iverilog
## Iverilog is an open-source Verilog simulation tool. If you don’t have it installed yet, use this simple installation method.
```bash
$ sudo apt-get update
$ sudo apt-get install iverilog

```
### The apt-get install iverilog command directly installs the Iverilog package from the Ubuntu repositories, so it's a straightforward installation. Iverilog is a tool that helps in simulating Verilog hardware description code, an important part of the RISC-V development process.

<img width="949" height="478" alt="image" src="https://github.com/user-attachments/assets/7b6cac05-08fa-45aa-bf65-2c845610d883" />

#### <ins>**gtkwave**</ins>
### Installing GTKWAVE
### GTKWAVE is a waveform viewer used to visualize simulation results. Follow the steps below to install it.
```bash
$ sudo apt-get update
$ sudo apt install gtkwave
```
### The gtkwave tool allows you to visualize the results of your hardware simulations (from Iverilog, for example) and is essential for debugging and analysis. This installation will ensure you can view the waveform outputs in an easy-to-read interface.
<img width="753" height="281" alt="image" src="https://github.com/user-attachments/assets/c97764d6-efee-49b9-b3b2-da11d49c3cf5" />




