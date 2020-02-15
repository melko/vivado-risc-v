# vivado-risc-v

# Xilinx Vivado block designs for FPGA RISC-V SoC running Debian Linux distro.

This repository helps to create RISC-V SoC HDL, Vivado project, FPGA bitstream and bootable SD card.
The SD card contains Berkeley Boot Loader, U-Boot, 5.4 Linux kernel and Debian root FS.
Linux package repositories and regular updates are provided by Debian.

Also can be used to run bare-metal or RTOS.

# Prerequisites

## Hardware
Xilinx VC707 or Digilent Nexys Video board.
VC707 allows to prototype more powerful system: 2X cores (4 vs 2),
2X memory (1GB vs 512MB), 2X CPU clock frequency (100MHz vs 50MHz).
Nexys Video is several time less expensive, academic discount is avaialble.

## Workstation
Ubuntu 18 LTS machine is recommended.
sudo access required.

## Software
Vivado 2019.1. Nexys Video is supported by free version of Vivado. VC707 requires Vivado license.

# Usage
```
sudo apt update
sudo apt install git make gcc-riscv64-linux-gnu
git clone https://github.com/eugene-tarassov/vivado-risc-v.git
cd vivado-risc-v
make update-submodules
source /opt/Xilinx/Vivado/2019.1/settings64.sh
make CONFIG=rocket64b2 BOARD=nexys-video bitstream
make linux bbl
```
Use USB SD card reader to connect SD card to the workstation, and run:
```
./mk-sd-card
```
The script looks for USB memory device and asks confirmation before using it.
Make sure to confirm right SD card device - all old data will be erased.

# Prebuild images

Prebuild FPGA bitstream for Nexy Video board, and SD card image are available in releases area.

# Notes

RISC-V SoC in this repo contains bootrom, which differ from original Rocket Chip bootrom.
The modified bootrom contains SD card boot loader and extended device tree.

RISC-V SoC in this repo contains DDR, UART, SD and Ethernet controllers.
DDR and UART are provided by Vivado, SD and Ethernet are open source Verilog.

Linux kernel and U-Boot use device tree, which is stored in RISC-V bootrom in FPGA.

Same SD card will work on any board or RISC-V configuration.

Nexys Video board can be configured to load FPGA bitstream from SD card.

The device tree contains Ethernet MAC address, which is not unique.
It might be necessary to rebuild bitstream with different MAC, see Makefile for details.

If not using provided SD card image: RISC-V bootrom loads and executes boot.elf file from SD card DOS partition.
boot.elf is regular executable ELF, it can contain any software suitable for RISC-V RV64 M mode.
In case of Linux boot, boot.elf contains Berkeley Boot Loader and U-Boot.

The Makefile creates Vivado project directory, e.g. workspace/rocket64b2/vivado-nexys-video-riscv.
You can open the project in Vivado GUI to see RISC-V SoC structure, make changes, add peripherals, and rebuild the bitstream.

RISC-V SoC in this repo uses BSCAN block to support both RISC-V debugging and FPGA access over same JTAG cable.