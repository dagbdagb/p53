# Lenovo p53 adventures

The Lenovo P53 has :
- dual TB3 ports with 4 PCIE 3.0 lanes each
- 3x NVME slots with 4 PCIE 3.0 lanes each
- Onboard Quadro RTX 3000 
  (which interestingly is not an RTX 3000-series GPU. It is Turing, not Ampere)

In short: it has plenty ports for connecting GPUs. If it can be made to work for that purpose is a different story.

My current config permits 2x RTX 3090 and the onboard Quadro card.


I have made various permutations of kernel config and options to enable 2x RTX 3090 
on a Lenovo P53. Each directory here represents a snapshot of dmesg/lspci/kernel commandline etc for each.
I have made an effort to enable resizable BAR for the Ampere GPUs, but that is more a case of 'because we can'.
It does not make up for the 4x path.

Resizable BAR requires 'Above 4G decoding' enabled in BIOS, which is the case for the P53. (Not a visible option.)
On Windows, support for resizable BAR must also be present in the motherboard BIOS. 
And this, in turn, appears to require a gen 10 or better CPU. For some reason. Workarounds may exist.

Linux has 'native support' for resizable BAR as long as 'Above 4G decoding' is present. So not bound by what 
the BIOS offers.

Finally, the GPU VBIOS must also support resizable BAR. Applies to both Windows and Linux.
Officially, this only exists for Ampere and up. Workarounds may exist.

For now, I manage to enable resizable BAR for one GPU. (Well, both are resizable, but only one scales up all the way.)

```
p53 ~ # nvidia-smi
Fri Apr 19 14:03:09 2024
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 550.67                 Driver Version: 550.67         CUDA Version: 12.4     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  Quadro RTX 3000                Off |   00000000:01:00.0 Off |                  N/A |
| N/A   32C    P8              1W /   80W |       1MiB /   6144MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
|   1  NVIDIA GeForce RTX 3090        Off |   00000000:09:00.0 Off |                  N/A |
|  0%   41C    P8             16W /  370W |       2MiB /  24576MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
|   2  NVIDIA GeForce RTX 3090        Off |   00000000:2F:00.0 Off |                  N/A |
| 31%   24C    P8             12W /  350W |       2MiB /  24576MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|  No running processes found                                                             |
+-----------------------------------------------------------------------------------------+
```


```
09:00.0 VGA compatible controller: NVIDIA Corporation GA102 [GeForce RTX 3090] (rev a1) (prog-if 00 [VGA controller])
	Subsystem: Gigabyte Technology Co., Ltd GA102 [GeForce RTX 3090]
	Flags: bus master, fast devsel, latency 0, IRQ 152
	Memory at a2000000 (32-bit, non-prefetchable) [size=16M]
	Memory at 4010000000 (64-bit, prefetchable) [size=256M]
	Memory at 4020000000 (64-bit, prefetchable) [size=32M]
	I/O ports at 4000 [size=128]
	Capabilities: [60] Power Management version 3
	Capabilities: [68] MSI: Enable+ Count=1/1 Maskable- 64bit+
	Capabilities: [78] Express Legacy Endpoint, IntMsgNum 0
	Capabilities: [b4] Vendor Specific Information: Len=14 <?>
	Capabilities: [100] Virtual Channel
	Capabilities: [250] Latency Tolerance Reporting
	Capabilities: [258] L1 PM Substates
	Capabilities: [128] Power Budgeting <?>
	Capabilities: [420] Advanced Error Reporting
	Capabilities: [600] Vendor Specific Information: ID=0001 Rev=1 Len=024 <?>
	Capabilities: [900] Secondary PCI Express
	Capabilities: [bb0] Physical Resizable BAR
	Capabilities: [c1c] Physical Layer 16.0 GT/s <?>
	Capabilities: [d00] Lane Margining at the Receiver
	Capabilities: [e00] Data Link Feature <?>
	Kernel driver in use: nvidia
	Kernel modules: nvidia_drm, nvidia


	Capabilities: [bb0 v1] Physical Resizable BAR
		BAR 0: current size: 16MB, supported: 16MB
		BAR 1: current size: 256MB, supported: 64MB 128MB 256MB 512MB 1GB 2GB 4GB 8GB 16GB 32GB
		BAR 3: current size: 32MB, supported: 32MB
```


```
2f:00.0 VGA compatible controller: NVIDIA Corporation GA102 [GeForce RTX 3090] (rev a1) (prog-if 00 [VGA controller])
	Subsystem: Hewlett-Packard Company GA102 [GeForce RTX 3090]
	Flags: bus master, fast devsel, latency 0, IRQ 153
	Memory at af000000 (32-bit, non-prefetchable) [size=16M]
	Memory at 5800000000 (64-bit, prefetchable) [size=32G]
	Memory at 5400000000 (64-bit, prefetchable) [size=32M]
	I/O ports at 7000 [size=128]
	Expansion ROM at ae800000 [virtual] [disabled] [size=512K]
	Capabilities: [60] Power Management version 3
	Capabilities: [68] MSI: Enable+ Count=1/1 Maskable- 64bit+
	Capabilities: [78] Express Legacy Endpoint, IntMsgNum 0
	Capabilities: [b4] Vendor Specific Information: Len=14 <?>
	Capabilities: [100] Virtual Channel
	Capabilities: [250] Latency Tolerance Reporting
	Capabilities: [258] L1 PM Substates
	Capabilities: [128] Power Budgeting <?>
	Capabilities: [420] Advanced Error Reporting
	Capabilities: [600] Vendor Specific Information: ID=0001 Rev=1 Len=024 <?>
	Capabilities: [900] Secondary PCI Express
	Capabilities: [bb0] Physical Resizable BAR
	Capabilities: [c1c] Physical Layer 16.0 GT/s <?>
	Capabilities: [d00] Lane Margining at the Receiver
	Capabilities: [e00] Data Link Feature <?>
	Kernel driver in use: nvidia
	Kernel modules: nvidia_drm, nvidia

	Capabilities: [bb0 v1] Physical Resizable BAR
		BAR 0: current size: 16MB, supported: 16MB
		BAR 1: current size: 32GB, supported: 64MB 128MB 256MB 512MB 1GB 2GB 4GB 8GB 16GB 32GB
		BAR 3: current size: 32MB, supported: 32MB
```


