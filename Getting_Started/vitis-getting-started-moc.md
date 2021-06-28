# Getting Started with Vitis Vector Addition Example on MOC

This guide provides step-by-step instructions on getting started with the Vitis vector addition example using Mass Open Cloud (MOC) and CloudLab. The example is customized version of the [getting started example published by Xilinx](https://github.com/Xilinx/Vitis-Tutorials/tree/master/Getting_Started/Vitis). Before you start, it is expected that you read part 1 and 3 of this tutorial. Part 2 is about installing tools which can be skipped because we have already provided an MOC image with tools (Vitis and some dependencies) installed. The original example doesn't have a configuration file for U280 card which we use in CloudLab. This file has been added in the customized version. The source code and the configuration file used for this tutorial can be found [here](https://github.com/OCT-FPGA/Vitis-Tutorials-U280/tree/master/Getting_Started/src). 

For issues and fixes, please visit [this page](https://github.com/OCT-FPGA/oct-tutorials/blob/main/issues-and-fixes.md).
	
## Tools

Xilinx Vitis 2020.1 or 2020.2

## Prerequisites

It is assumed that the user has already followed [these instructions](https://github.com/OCT-FPGA/oct-tutorials/blob/main/mocsetup/instancesetup.md) to create an MOC instance and booted it up. This is a one time process. Once your instance is created, it will remain active unless you manually delete it using the MOC web interface. Whenever you want to log in to this instance, you can do so by using any SSH client like PuTTY, and entering information such as IP address and SSH private key etc. Refer to [Set up SSH access](https://github.com/OCT-FPGA/oct-tutorials/tree/main/vncsshsetup#1-set-up-ssh-access) for details. It is highly recommended that you [Set up VNC](https://github.com/OCT-FPGA/oct-tutorials/tree/main/vncsshsetup#2-set-up-vnc) to successfully complete the tutorial. The reason for this is that if you use just PuTTY to run build commands, and in case the PuTTY session gets disconnected while the build is running, the build process will be interrupted, and you won't get the bitstream. Using VNC is considered safe due to this reason.  

## Clone the repository

Open up a terminal on the booted MOC instance and enter the following command to clone the repository.

```bash
git clone https://github.com/OCT-FPGA/Vitis-Tutorials-U280.git
```

## Setting up the environment

* To configure the environment to run Vitis, source the following scripts:

```bash
source /tools/Xilinx/Vitis/2020.1/settings64.sh
source /opt/xilinx/xrt/setup.sh
```

* Then make sure the following environment variable is correctly set to point to the your U280 platform installation directory.

```bash
export PLATFORM_REPO_PATHS=/opt/xilinx/platforms/xilinx_u280_xdma_201920_3/
```

* In addition, set the LIBRARY_PATH environment variable.
```bash
export LIBRARY_PATH=/usr/lib/x86_64-linux-gnu
```

These ```source``` and ```export``` commands can be included in the the user's .bashrc file located in the home directory (```~/.bashrc```), so you won't have to repeat these steps every time you log in. If you use .bashrc, you should include them below the following statement.

```bash
case $- in
    *i*) ;;
      *) return;;
esac
```

## Build and run

All build and run instructions are based on https://github.com/Xilinx/Vitis-Tutorials/blob/master/Getting_Started/Vitis/Part4.md except for some platform specific changes. If you need to know the details of how build commands work, please refer to the original guilde. 

### Software emulation

```bash
cd <Path to the cloned repo>/Getting_Started/Vitis/example/u280/sw_emu

g++ -Wall -g -std=c++11 ../../src/host.cpp -o app.exe -I${XILINX_XRT}/include/ -L${XILINX_XRT}/lib/ -lOpenCL -lpthread -lrt -lstdc++
emconfigutil --platform xilinx_u280_xdma_201920_3 --nd 1
v++ -c -t sw_emu --config ../../src/u280.cfg -k vadd -I../../src ../../src/vadd.cpp -o vadd.xo 
v++ -l -t sw_emu --config ../../src/u280.cfg ./vadd.xo -o vadd.xclbin
```

After building the software emulation, run the following commands. This will run the application in software emulation mode.
```bash
export XCL_EMULATION_MODE=sw_emu
./app.exe
```
You should be able to see the following output if you ran the application successfully.
```bash
INFO: Found Xilinx Platform
INFO: Loading 'vadd.xclbin'
TEST PASSED
```

### Hardware emulation

After running software emulation, enter the following commands to run hardware emulation.

```bash
cd ../hw_emu

g++ -Wall -g -std=c++11 ../../src/host.cpp -o app.exe -I${XILINX_XRT}/include/ -L${XILINX_XRT}/lib/ -lOpenCL -lpthread -lrt -lstdc++
emconfigutil --platform xilinx_u280_xdma_201920_3 --nd 1
v++ -c -t hw_emu --config ../../src/u280.cfg -k vadd -I../../src ../../src/vadd.cpp -o vadd.xo 
v++ -l -t hw_emu --config ../../src/u280.cfg ./vadd.xo -o vadd.xclbin
```

After building the hardware emulation, run the following commands. This will run the application in hardware emulation mode.
```bash
export XCL_EMULATION_MODE=hw_emu
./app.exe
```

If the application is run successfully, you will see TEST PASSED on the terminal.

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

### Hardware

The last step is to build the application to run on actual U280 hardware. Note that hardware build can take several hours to complete. You can optionally set the number of jobs to run in order to reduce the build time. For example if you pass ```--jobs 8```, eight jobs will run simultaneously in the build process. This parameter needs to be chosen by taking into account the number of CPU cores that your instance has.   

```bash
cd ../hw

g++ -Wall -g -std=c++11 ../../src/host.cpp -o app.exe -I${XILINX_XRT}/include/ -L${XILINX_XRT}/lib/ -lOpenCL -lpthread -lrt -lstdc++
v++ -c -t hw --config ../../src/u280.cfg -k vadd -I../../src ../../src/vadd.cpp -o vadd.xo 
v++ -l -t hw --config ../../src/u280.cfg ./vadd.xo -o vadd.xclbin # optionally pass <--jobs <NUM_OF_JOBS>> here.
```
To run the application on hardware, you need to copy app.exe and vadd.xclbin to the CloudLab server which hosts the U280. For this, you need to have the private key of the CloudLab server stored on MOC. 

#### Setting up the CloudLab private key

You should already have a private key to access the CloudLab server from your home computer. This key can be stored on MOC, so that you can connect to CloudLab from MOC. Make sure that you use the OpenSSH format of the private key instead of .ppk for both MOC and CloudLab. [This guide](https://github.com/OCT-FPGA/oct-tutorials/blob/main/key-conversion/key-conversion.md) shows how to convert a PuTTY .ppk private key to OpenSSH format. If you use Windows, the following command in the command prompt will copy this key to MOC.

```bash
scp -i <path to MOC private key (OpenSSH)> <path to CloudLab private key (OpenSSH)> ubuntu@<MOC IP>:~/.ssh/
```

Example:

```bash
scp -i C:\Users\Suranga\Desktop\keys\moc_openssh C:\Users\Suranga\Desktop\keys\cloudlab_openssh ubuntu@128.31.25.145:~/.ssh/ 
```
After you copied this key to MOC, you should set the appropriate file permissions. Otherwise you won't be able to connect to the CloudLab server.

```bash
chmod 700 ~/.ssh/cloudlab_openssh
```
Now, you can copy all necessary files from MOC to CloudLab and run programs on the FPGA. We need to copy two files; the bitstream (vadd.xclbin) and the application binary (app.exe). 

```bash
scp -i <path to CloudLab private key on MOC> vadd.xclbin app.exe <CloudLab Username>@<CloudLab IP>:~
```

Example:

```bash
scp -i ~/.ssh/cloudlab_openssh app.exe vadd.xclbin suranga@pc153.cloudlab.umass.edu:~
```

Then, depending on your preference, you can choose one of the following two methods to run the program.

#### 1. Local execution

After copying the files, log in to the CloudLab server (The instructions for setting up the CloudLab server for FPGA experiments are available [here](https://github.com/OCT-FPGA/oct-tutorials/tree/main/cloudlab-setup). Make sure that ```XILINX_XRT``` environment variable is set by running

```bash
source /opt/xilinx/xrt/setup.sh
```
Then run

```bash
./app.exe
```

#### 2. Remote execution

In this approach you can run the following command from within MOC.

```bash
ssh -i <path to CloudLab private key on MOC> <CloudLab Username>@<CloudLab IP> "source <path to XRT>; <path to application>"
```

Example:

```bash
ssh -i ~/.ssh/cloudlab_openssh suranga@pc153.cloudlab.umass.edu "source /opt/xilinx/xrt/setup.sh; ./app.exe"

```

Similar to software and hardware emulation result, you should see TEST PASSED on the terminal.
