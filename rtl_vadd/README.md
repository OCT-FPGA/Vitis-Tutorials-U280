# RTL Vector Addition Example on U280

This guide provides step by step instructions to build and run the RTL vector addition example in https://github.com/Xilinx/Vitis_Accel_Examples on a Xilinx U280 target. 


## Prerequisites

Access to a build machine equipped with Vitis 2023.1 is required. If you don't have access to one, we can offer assistance in providing access. Please refer to the instructions provided at [this link](https://github.com/OCT-FPGA/OCT-Tutorials/blob/master/nercsetup/nerc-vm-guide.md).

## Tools

- Vitis 2022.2 or Vitis 2023.1

## 1. Clone the repository

```bash
git clone https://github.com/Xilinx/Vitis_Accel_Examples
```

## 2. Build

Make sure that ```XILINX_VITIS``` and ```XILINX_XRT``` environment variables are set. This can be done by

```bash
source /tools/Xilinx/Vitis/2023.1/settings64.sh
```

```bash
source /opt/xilinx/xrt/setup.sh
```

Then, navigate to the rtl_vadd directory.

```bash
cd Vitis_Accel_Examples/rtl_kernels/rtl_vadd
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
./rtl_vadd ./build_dir.sw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
```

Output:

```bash
Found Platform
Platform Name: Xilinx
INFO: Reading build_dir.sw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
Loading: 'build_dir.sw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin'
CRITICAL WARNING: [SW-EM 09-0] Unable to find emconfig.json. Using default device "xilinx:pcie-hw-em:7v3:1.0"
Trying to program device[0]: xilinx:pcie-hw-em:7v3:1.0
Kernel Name: krnl_vadd_rtl_1, CU Number: 0, Thread creation status: success
Device[0]: program successful!
Kernel Name: krnl_vadd_rtl_1, CU Number: 0, State: Start
Kernel Name: krnl_vadd_rtl_1, CU Number: 0, State: Running
Kernel Name: krnl_vadd_rtl_1, CU Number: 0, State: Idle
TEST PASSED
device process sw_emu_device done
Kernel Name: krnl_vadd_rtl_1, CU Number: 0, Status: Shutdown
```

- HW emulation 

```bash
export XCL_EMULATION_MODE=hw_emu
```

```bash
./rtl_vadd ./build_dir.hw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
```

Output:
```bash
Found Platform
Platform Name: Xilinx
INFO: Reading build_dir.hw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
Loading: 'build_dir.hw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin'
CRITICAL WARNING: [HW-EMU 08-0] Unable to find emconfig.json. Using default device "xilinx:pcie-hw-em:7v3:1.0"
Trying to program device[0]: xilinx:pcie-hw-em:7v3:1.0
INFO: [HW-EMU 05] Path of the simulation directory : /home/ubuntu/Vitis_Accel_Examples/rtl_kernels/rtl_vadd/.run/10997/hw_emu/device0/binary_0/behav_waveform/xsim

 server socket name is	/tmp/ubuntu/device0_0_10997
INFO: [HW-EMU 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. The flow uses approximate models for Global memories and interconnect and hence the performance data generated is approximate.
configuring penguin scheduler mode
scheduler config ert(0), dataflow(0), slots(16), cudma(1), cuisr(0), cdma(0), cus(1)
Device[0]: program successful!
TEST PASSED
INFO: [HW-EMU 06-0] Waiting for the simulator process to exit
INFO: [HW-EMU 06-1] All the simulator processes exited successfully
INFO: [HW-EMU 07-0] Please refer the path "/home/ubuntu/Vitis_Accel_Examples/rtl_kernels/rtl_vadd/.run/10997/hw_emu/device0/binary_0/behav_waveform/xsim/simulate.log" for more detailed simulation infos, errors and warnings.
```

### 3.2 Run on FPGA hardware

To run on FPGA hardware, copy the xclbin and the executable (rtl_vadd) to the cloudlab server. Before doing this, make sure you place the CloudLab private key in the ~/.ssh/ directory on the NERC machine. If your CloudLab private key was generated using PuTTY, it needs to be converted to OpenSSH format before being placed on the NERC machine. Follow [these](https://github.com/OCT-FPGA/OCT-Tutorials/blob/master/managing-keys/key-conversion.md) instructions for key conversion. 

For example, if you want to copy these files to the home directory of pc151.cloudlab.umass.edu, you use the following command:

```bash
scp -i ~/.ssh/<your cloudlab private key> rtl_vadd ./build_dir.hw.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin <your user name>@pc151.cloudlab.umass.edu:~
```

On the CloudLab server run the application.

```bash
./rtl_vadd vadd.xclbin
```
You should see "TEST PASSED" printed on the terminal.

```bash
Found Platform
Platform Name: Xilinx
INFO: Reading vadd.xclbin
Loading: 'vadd.xclbin'
Trying to program device[0]: xilinx_u280_gen3x16_xdma_base_1
Device[0]: program successful!
TEST PASSED
```
