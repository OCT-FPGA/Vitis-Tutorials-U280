# Getting Started with Vitis Vector Addition Example on MOC

This guide provides step-by-step instructions on getting started with the Vitis vector addition example using Mass Open Cloud (MOC) and CloudLab. The example is customized version of the [getting started example published by Xilinx](https://github.com/Xilinx/Vitis-Tutorials/tree/master/Getting_Started/Vitis). Before you start, it is expected that you read part 1 and 3 of this tutorial. Part 2 is about installing tools which can be skipped because we have already provided an MOC image with tools (Vitis and some dependencies) installed. The original example doesn't have a configuration file for U280 card which we use in CloudLab. This file has been added in the customized version. The source code and the configuration file used for this tutorial can be found [here](https://github.com/OCT-FPGA/Vitis-Tutorials-U280/tree/master/Getting_Started/src). 

For issues and fixes, please visit [this page](https://github.com/OCT-FPGA/oct-tutorials/blob/main/issues-and-fixes.md).
	
## Tools

Xilinx Vitis 2020.1 or 2020.2

## Prerequisites

- Set up an MOC VM

It is assumed that you have already followed [these instructions](https://github.com/OCT-FPGA/oct-tutorials/blob/main/mocsetup/instancesetup.md) to create an MOC instance (VM) and booted it up. This is a one time process. Once your instance is created, it will remain active unless you delete it using the MOC web interface or OpenStack CLI. Whenever you want to log in to this instance, you can do so by entering information such as IP address and SSH private key using any SSH client such has PuTTY. Refer to [Set up SSH access](https://github.com/OCT-FPGA/oct-tutorials/tree/main/vncsshsetup#1-set-up-ssh-access) for details on how to do this. It is highly recommended that you [set up VNC access](https://github.com/OCT-FPGA/oct-tutorials/tree/main/vncsshsetup#2-set-up-vnc) to successfully complete this tutorial. The reason for this is that if you use just PuTTY to run Vitis build commands, and your PuTTY session gets disconnected while the build is running, the build process will be interrupted, and you will have to start over. Using VNC is recommended because the build process will run uninterrupted regardless of the status of the PuTTY session.

- Start a CloudLab experiment

You need to set up a CloudLab experiment according to [these instructions](https://github.com/OCT-FPGA/oct-tutorials/blob/main/cloudlab-setup/README.md) before you start running on hardware. Note that you don't need to set up the experiment until [Section 3.3.1](https://github.com/OCT-FPGA/Vitis-Tutorials-U280/blob/master/Getting_Started/vitis-getting-started-moc.md#331-local-execution).

## 1. Clone the repository

The first step is to open up a terminal in the booted MOC instance and enter the following command to clone the repository.

```bash
git clone https://github.com/OCT-FPGA/Vitis-Tutorials-U280.git
```

## 2. Set up the environment

* To configure the environment to run Vitis commands, run the following shell commands.

```bash
source /tools/Xilinx/Vitis/2020.1/settings64.sh
source /opt/xilinx/xrt/setup.sh
```

* Also make sure that ```PLATFORM_REPO_PATHS``` environment variable is correctly set to point to your U280 platform installation directory.

```bash
export PLATFORM_REPO_PATHS=/opt/xilinx/platforms/xilinx_u280_xdma_201920_3/
```

* In addition, set the ```LIBRARY_PATH``` environment variable.

```bash
export LIBRARY_PATH=/usr/lib/x86_64-linux-gnu
```

These ```source``` and ```export``` commands can be included in the the user .bashrc file located in the home directory (```~/.bashrc```), so you won't have to repeat these steps every time you log in to the VM. If you use .bashrc, you should include these commands below the following statement.

```bash
case $- in
    *i*) ;;
      *) return;;
esac
```

## 3. Build and run

All build and run instructions are based on https://github.com/Xilinx/Vitis-Tutorials/blob/master/Getting_Started/Vitis/Part4.md except for some platform specific changes. If you need to know the details of how these commands work, please refer to the original guilde. 

### 3.1 Software emulation

Enter the following commands to run software emulation.

```bash
cd <Path to the cloned repo>/Getting_Started/Vitis/example/u280/sw_emu

g++ -Wall -g -std=c++11 ../../src/host.cpp -o app.exe -I${XILINX_XRT}/include/ -L${XILINX_XRT}/lib/ -lOpenCL -lpthread -lrt -lstdc++
emconfigutil --platform xilinx_u280_xdma_201920_3 --nd 1
v++ -c -t sw_emu --config ../../src/u280.cfg -k vadd -I../../src ../../src/vadd.cpp -o vadd.xo 
v++ -l -t sw_emu --config ../../src/u280.cfg ./vadd.xo -o vadd.xclbin
```

These commands will create a host application binary (app.exe) and an FPGA binary for software emulation. The next step is to tun the application in software emulation mode.

```bash
export XCL_EMULATION_MODE=sw_emu
./app.exe
```

You should be able to see the following output if the application was run successfully.

```bash
INFO: Found Xilinx Platform
INFO: Loading 'vadd.xclbin'
TEST PASSED
```

### 3.2 Hardware emulation

After running software emulation, enter the following commands to run hardware emulation.

```bash
cd ../hw_emu

g++ -Wall -g -std=c++11 ../../src/host.cpp -o app.exe -I${XILINX_XRT}/include/ -L${XILINX_XRT}/lib/ -lOpenCL -lpthread -lrt -lstdc++
emconfigutil --platform xilinx_u280_xdma_201920_3 --nd 1
v++ -c -t hw_emu --config ../../src/u280.cfg -k vadd -I../../src ../../src/vadd.cpp -o vadd.xo 
v++ -l -t hw_emu --config ../../src/u280.cfg ./vadd.xo -o vadd.xclbin
```

Similar to what you did in software emulation, the the generated executable.

```bash
export XCL_EMULATION_MODE=hw_emu
./app.exe
```

You should be able to see the hardware emulation output on the terminal.

```bash
INFO: Found Xilinx Platform
INFO: Loading 'vadd.xclbin'
INFO: [HW-EM 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. The flow uses approximate models for DDR memory and interconnect and hence the performance data generated is approximate.
TEST PASSED
INFO::[ Vitis-EM 22 ] [Time elapsed: 0 minute(s) 27 seconds, Emulation time: 0.0510519 ms]
Data transfer between kernel(s) and global memory(s)
vadd_1:m_axi_aximm1-DDR[1]          RD = 16.000 KB              WR = 16.000 KB
vadd_1:m_axi_aximm2-DDR[1]          RD = 16.000 KB              WR = 0.000 KB

INFO: [HW-EM 06-0] Waiting for the simulator process to exit
INFO: [HW-EM 06-1] All the simulator processes exited successfully
```

### 3.3 Hardware

The last step is to build the application to run on actual U280 FPGA hardware. Note that hardware build can take several hours to complete. You can optionally set the number of jobs to run in order to reduce the build time. For example if you set ```--jobs 8``` as an additional argument to the Vitis build command (this is supported in Vitis versions 2020.2 or earlier), eight jobs will run simultaneously in the build process. This parameter needs to be chosen by taking into account the number of CPU cores that your instance has.   

```bash
cd ../hw

g++ -Wall -g -std=c++11 ../../src/host.cpp -o app.exe -I${XILINX_XRT}/include/ -L${XILINX_XRT}/lib/ -lOpenCL -lpthread -lrt -lstdc++
v++ -c -t hw --config ../../src/u280.cfg -k vadd -I../../src ../../src/vadd.cpp -o vadd.xo 
v++ -l -t hw --config ../../src/u280.cfg ./vadd.xo -o vadd.xclbin # optionally pass <--jobs <NUM_OF_JOBS>> here.
```
#### 3.3.1 Copy executables to CloudLab

To run the application on FPGA hardware, you need to copy app.exe and vadd.xclbin to the CloudLab server which hosts the U280. For this, you need to have the private key of the CloudLab server stored on MOC. For information on setting up SSH keys, refer to [this guide](https://github.com/OCT-FPGA/oct-tutorials/blob/main/managing-keys/setup-keys.md). Once the SSH access is set up, you can copy the necessary files from MOC to CloudLab and run programs on FPGA hardware. We need to copy two files; the bitstream (vadd.xclbin) and the application binary (app.exe). 

```bash
scp -i <path to CloudLab private key on MOC> vadd.xclbin app.exe <CloudLab Username>@<CloudLab IP>:~
```

Example:

```bash
scp -i ~/.ssh/cloudlab_openssh app.exe vadd.xclbin suranga@pc153.cloudlab.umass.edu:~
```

Then, depending on your preference, you can choose one of the following two methods to run the program.

#### 3.3.1. Local execution

After copying the files, log in to the CloudLab server (The instructions for setting up the CloudLab server for FPGA experiments are available [here](https://github.com/OCT-FPGA/oct-tutorials/tree/main/cloudlab-setup). Make sure that ```XILINX_XRT``` environment variable is set by running

```bash
source /opt/xilinx/xrt/setup.sh
```
Then run

```bash
./app.exe
```

#### 3.3.2. Remote execution

In this approach you can run the following command from within MOC.

```bash
ssh -i <path to CloudLab private key on MOC> <CloudLab Username>@<CloudLab IP> "source <path to XRT>; <path to application>"
```

Example:

```bash
ssh -i ~/.ssh/cloudlab_openssh suranga@pc153.cloudlab.umass.edu "source /opt/xilinx/xrt/setup.sh; ./app.exe"

```

Similar to software and hardware emulation result, you should see TEST PASSED on the terminal.
