# Vitis Accel Hello World Example on U280

This guide provides step by step instructions to build and run the hello world (vector addition) example in https://github.com/Xilinx/Vitis_Accel_Examples on a Xilinx U280 target. 

## Tools

- Vitis 2020.2

## 1. Clone the repository

```bash
git clone https://github.com/Xilinx/Vitis_Accel_Examples
```

## 2. Build

Make sure that ```XILINX_VITIS``` and ```XILINX_XRT``` environment variables are set. This can be done by

```bash
source /tools/Xilinx/Vitis/2020.2/settings64.sh
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
make all TARGET=sw_emu DEVICE=/opt/xilinx/platforms/xilinx_u280_xdma_201920_3/xilinx_u280_xdma_201920_3.xpfm
```

### 2.2 HW emulation

```bash
make all TARGET=hw_emu DEVICE=/opt/xilinx/platforms/xilinx_u280_xdma_201920_3/xilinx_u280_xdma_201920_3.xpfm
```

### 2.3 HW

```bash
make all TARGET=hw DEVICE=/opt/xilinx/platforms/xilinx_u280_xdma_201920_3/xilinx_u280_xdma_201920_3.xpfm
```


## 4. Run application

### 4.1 Run software and hardware emulation

- SW emulation 

```bash
export XCL_EMULATION_MODE=sw_emu
```

```bash
./hello_world ./build_dir.sw_emu.xilinx_u280_xdma_201920_3/vadd.xclbin
```

Output:

```bash
Found Platform
Platform Name: Xilinx
INFO: Reading ./build_dir.sw_emu.xilinx_u280_xdma_201920_3/vadd.xclbin
Loading: './build_dir.sw_emu.xilinx_u280_xdma_201920_3/vadd.xclbin'
CRITICAL WARNING: [SW-EM 09-0] Unable to find emconfig.json. Using default device "xilinx:pcie-hw-em:7v3:1.0"
Trying to program device[0]: xilinx:pcie-hw-em:7v3:1.0
Device[0]: program successful!
TEST PASSED
The maximum depth reached by any of the 3 hls::stream() instances in the design is 256

```

- HW emulation 

```bash
export XCL_EMULATION_MODE=hw_emu
```

```bash
./hello_world ./build_dir.hw_emu.xilinx_u280_xdma_201920_3/vadd.xclbin
```

Output:
```bash
Found Platform
Platform Name: Xilinx
INFO: Reading ./build_dir.hw_emu.xilinx_u280_xdma_201920_3/vadd.xclbin
Loading: './build_dir.hw_emu.xilinx_u280_xdma_201920_3/vadd.xclbin'
CRITICAL WARNING: [HW-EMU 08-0] Unable to find emconfig.json. Using default device "xilinx:pcie-hw-em:7v3:1.0"
Trying to program device[0]: xilinx:pcie-hw-em:7v3:1.0
INFO: [HW-EMU 01] Hardware emulation runs simulation underneath. Using a large data set will result in long simulation times. It is recommended that a small dataset is used for faster execution. The flow uses approximate models for DDR memory and interconnect and hence the performance data generated is approximate.
Device[0]: program successful!
TEST PASSED
INFO::[ Vitis-EM 22 ] [Time elapsed: 0 minute(s) 20 seconds, Emulation time: 0.0277806 ms]
Data transfer between kernel(s) and global memory(s)
vadd_1:m_axi_gmem0-HBM[0]          RD = 16.000 KB              WR = 16.000 KB
vadd_1:m_axi_gmem1-HBM[0]          RD = 16.000 KB              WR = 0.000 KB

INFO: [HW-EMU 06-0] Waiting for the simulator process to exit
INFO: [HW-EMU 06-1] All the simulator processes exited successfully
```

### 4.2 Run on FPGA hardware

To run on FPGA hardware, copy the xclbin and the application binary (hello_world) to the cloudlab server. For example, if you want to copy these files to the home directory of pc151.cloudlab.umass.edu the command is

```bash
scp -i ~/.ssh/<your cloudlab private key> hello_world ./build_dir.hw.xilinx_u280_xdma_201920_3/vadd.xclbin <your user name>@pc151.cloudlab.umass.edu:~
```

On the Cloudlab server make sure ```XILINX_XRT``` environment variable is set (see step 3). Then run

```bash
./hello_world vadd.xclbin
```
and you should see "TEST PASSED" on the terminal similar to sw and hw emulation result.

```bash
Found Platform
Platform Name: Xilinx
INFO: Reading vadd.xclbin
Loading: 'vadd.xclbin'
Trying to program device[0]: xilinx_u280_xdma_201920_3
Device[0]: program successful!
TEST PASSED
```

## Issues and fixes


- If you get the following error

```bash
fatal error: CL/cl2.hpp: No such file or directory
```
then you will need to install OpenCL headers by running ```sudo apt install opencl-headers``` and then run ```make host```.


- You may get the warning "Permissions 0664 for '<your cloudlab private key>' are too open. It is required that your private key files are NOT accessible by others. This private key will be ignored." when trying to copy files to the target server.
  
  To fix this, you should change the permission level of the private key.
  
  ```bash
  sudo chmod 600 ~/.ssh/<your cloudlab private key>
  ```
