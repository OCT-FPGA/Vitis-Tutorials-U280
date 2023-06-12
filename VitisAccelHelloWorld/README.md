# Vitis Accel Hello World Example on U280

This guide provides step by step instructions to build and run the hello world (vector addition) example in https://github.com/Xilinx/Vitis_Accel_Examples on a Xilinx U280 target. 

## Tools

- Vitis 2022.2

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
./hello_world ./build_dir.sw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
```

Output:

```bash
Found Platform
Platform Name: Xilinx
INFO: Reading build_dir.sw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
Loading: 'build_dir.sw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin'
CRITICAL WARNING: [SW-EM 09-0] Unable to find emconfig.json. Using default device "xilinx:pcie-hw-em:7v3:1.0"
Trying to program device[0]: xilinx:pcie-hw-em:7v3:1.0
Kernel Name: vadd_1, CU Number: 0, Thread creation status: success
Device[0]: program successful!
Kernel Name: vadd_1, CU Number: 0, State: Start
Kernel Name: vadd_1, CU Number: 0, State: Running
Kernel Name: vadd_1, CU Number: 0, State: Idle
TEST PASSED
device process sw_emu_device done
Kernel Name: vadd_1, CU Number: 0, Status: Shutdown
INFO [HLS SIM]: The maximum depth reached by any hls::stream() instance in the design is 256
```

- HW emulation 

```bash
export XCL_EMULATION_MODE=hw_emu
```

```bash
./hello_world ./build_dir.sw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
```

Output:
```bash
Found Platform
Platform Name: Xilinx
INFO: Reading build_dir.hw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin
Loading: 'build_dir.hw_emu.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin'
CRITICAL WARNING: [HW-EMU 08-0] Unable to find emconfig.json. Using default device "xilinx:pcie-hw-em:7v3:1.0"
Trying to program device[0]: xilinx:pcie-hw-em:7v3:1.0
INFO: [HW-EMU 05] Path of the simulation directory : /home/ubuntu/Vitis_Accel_Examples/hello_world/.run/227428/hw_em/device0/binary_0/behav_waveform/xsim

 server socket name is	/tmp/ubuntu/device0_0_227428
INFO: [HW-EMU 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. The flow uses approximate models for Global memories and interconnect and hence the performance data generated is approximate.
configuring penguin scheduler mode
scheduler config ert(0), dataflow(1), slots(16), cudma(1), cuisr(0), cdma(0), cus(1)
Device[0]: program successful!
TEST PASSED
INFO: [HW-EMU 06-0] Waiting for the simulator process to exit
INFO: [HW-EMU 06-1] All the simulator processes exited successfully
INFO: [HW-EMU 07-0] Please refer the path "/home/ubuntu/Vitis_Accel_Examples/hello_world/.run/227428/hw_em/device0/binary_0/behav_waveform/xsim/simulate.log" for more detailed simulation infos, errors and warnings.
```

### 3.2 Run on FPGA hardware

To run on FPGA hardware, copy the xclbin and the executable (hello_world) to the cloudlab server. For example, if you want to copy these files to the home directory of pc151.cloudlab.umass.edu, you use the following command:

```bash
scp -i ~/.ssh/<your cloudlab private key> hello_world ./build_dir.hw.xilinx_u280_gen3x16_xdma_1_202211_1/vadd.xclbin <your user name>@pc151.cloudlab.umass.edu:~
```

On the Cloudlab server run the application.

```bash
./hello_world vadd.xclbin
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
