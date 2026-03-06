# Vitis Accel Hello World Example on U280

This guide provides step by step instructions to build and run the hello world (vector addition) example in https://github.com/Xilinx/Vitis_Accel_Examples on a Xilinx U280 target. 


## Prerequisites

Access to a build machine equipped with Vitis 2023.1 is required. If you don't have access to one, we can offer assistance in providing access. Please refer to the instructions provided [here](https://github.com/OCT-FPGA/OCT-Tutorials/blob/master/cloudlab-setup/build-machines.md).

## Tools

- Vitis 2022.2 or Vitis 2023.1

## 1. Clone the repository

```bash
git clone https://github.com/Xilinx/Vitis_Accel_Examples
```

## 2. Build

Make sure that ```XILINX_VITIS``` and ```XILINX_XRT``` environment variables are set. This can be done by

```bash
source /tools/Xilinx/Vitis/2022.2/settings64.sh
```

```bash
source /opt/xilinx/xrt/setup.sh
```

Then, navigate to the hello world directory.

```bash
cd Vitis_Accel_Examples/hello_world
```

### 2.1 SW emulation

```bash
make all TARGET=sw_emu PLATFORM=/opt/xilinx/platforms/xilinx_u280_gen3x16_xdma_1_202211_1/xilinx_u280_gen3x16_xdma_1_202211_1.xpfm
```

### 2.2 HW emulation

```bash
make all TARGET=hw_emu PLATFORM=/opt/xilinx/platforms/xilinx_u280_gen3x16_xdma_1_202211_1/xilinx_u280_gen3x16_xdma_1_202211_1.xpfm
```

### 2.3 HW build

```bash
make all TARGET=hw PLATFORM=/opt/xilinx/platforms/xilinx_u280_gen3x16_xdma_1_202211_1/xilinx_u280_gen3x16_xdma_1_202211_1.xpfm
```


## 3. Run application

### 3.1 Run software and hardware emulation

- SW emulation 

```bash
export XCL_EMULATION_MODE=sw_emu
```

```bash
./hello_world_xrt -x ./build_dir.sw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
```

Output:

```bash
Open the device0
Load the xclbin sw_emu/vadd.xclbin
Kernel Name: vadd_1, CU Number: 0, Thread creation status: success
Allocate Buffer in Global Memory
synchronize input buffer data to device global memory
Execution of the kernel
Kernel Name: vadd_1, CU Number: 0, State: Start
Kernel Name: vadd_1, CU Number: 0, State: Running
Kernel Name: vadd_1, CU Number: 0, State: Idle
Get the output data from the device
TEST PASSED
device process sw_emu_device done
Kernel Name: vadd_1, CU Number: 0, Status: Shutdown
INFO [HLS SIM]: The maximum depth reached by any hls::stream() instance in the design is 4096
```

- HW emulation 

```bash
export XCL_EMULATION_MODE=hw_emu
```

```bash
./hello_world_xrt -x ./build_dir.hw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
```

Output:
```bash
Open the device0
Load the xclbin hw_emu/vadd.xclbin
INFO: [HW-EMU 05] Path of the simulation directory : /users/ubuntu/Vitis-Tutorials-U280/VitisAccelHelloWorld/prebuilt/.run/13303/hw_emu/device0/binary_0/behav_waveform/xsim

 server socket name is  /tmp/ubuntu/device0_0_13303
INFO: [HW-EMU 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. The flow uses approximate models for Global memories and interconnect and hence the performance data generated is approximate.
configuring dataflow mode with ert polling
scheduler config ert(1), dataflow(1), slots(16), cudma(0), cuisr(0), cdma(0), cus(1)
Allocate Buffer in Global Memory
synchronize input buffer data to device global memory
Execution of the kernel
Get the output data from the device
TEST PASSED
INFO: [HW-EMU 06-0] Waiting for the simulator process to exit
INFO: [HW-EMU 06-1] All the simulator processes exited successfully
INFO: [HW-EMU 07-0] Please refer the path "/users/ubuntu/Vitis-Tutorials-U280/VitisAccelHelloWorld/prebuilt/.run/13303/hw_emu/device0/binary_0/behav_waveform/xsim/simulate.log" for more detailed simulation infos, errors and warnings.
```

### 3.2 Run on FPGA hardware

To run on FPGA hardware, copy the xclbin and the executable (hello_world) to the cloudlab server. Before doing this, make sure you place the CloudLab private key in the ~/.ssh/ directory on the NERC machine. If your CloudLab private key was generated using PuTTY, it needs to be converted to OpenSSH format before being placed on the NERC machine. Follow [these](https://github.com/OCT-FPGA/OCT-Tutorials/blob/master/managing-keys/key-conversion.md) instructions for key conversion. 

For example, if you want to copy these files to the home directory of pc151.cloudlab.umass.edu, you use the following command:

```bash
scp -i ~/.ssh/<your cloudlab private key> hello_world ./build_dir.hw.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin <your user name>@pc151.cloudlab.umass.edu:~
```

On the Cloudlab server run the application.

```bash
./hello_world_xrt -x vadd.xclbin
```
You should see "TEST PASSED" printed on the terminal.

```bash
Open the device0
Load the xclbin hw/vadd.xclbin
Allocate Buffer in Global Memory
synchronize input buffer data to device global memory
Execution of the kernel
Get the output data from the device
TEST PASSED
```
Note: If an error pops up, try unsetting the XCL_EMULATION_MODE variable.

```bash
unset XCL_EMULATION_MODE
```
