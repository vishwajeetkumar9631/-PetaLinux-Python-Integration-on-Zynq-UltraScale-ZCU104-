⚙️ ZCU104 Hardware Design & IP Configuration (Vivado)

Before building the PetaLinux project, a hardware platform must be created in Vivado and exported as an .xsa file. This file contains the FPGA design, block connections, and processor configuration required for PetaLinux.

🧩 Step 1: Create Vivado Project

Open Vivado 2023.2 (or version compatible with your PetaLinux tools).

Select Create Project → RTL Project → Do not specify sources at this time.

Choose ZCU104 as the target board or specify the part number:

xczu7ev-ffvc1156-2-e


Finish setup and open the Block Design window.

⚙️ Step 2: Add and Configure Processing System (PS)

From the IP catalog, add Zynq UltraScale+ MPSoC (Processing System).

Run Block Automation → select Apply Board Preset.
This automatically configures:

DDR4 memory interface

MIO & I/O peripherals

UART, I2C, SPI, and GPIO

PS-PL interfaces (AXI, IRQs, Clocks)

Verify key configurations under PS Configuration → PS-PL Interfaces:

Enable AXI HP0/HP1/HP2/HP3 ports for high-speed data exchange

Enable PL-to-PS interrupts if FPGA IPs will send events to the processor

Enable FPGA PL Fabric Clocks (FCLK0)

🧱 Step 3: Add Custom IPs (Optional)

If your design includes additional hardware (e.g., AI accelerators, filters, or custom logic):

Add your custom IP blocks to the design.

Connect them via AXI Interconnect to the PS AXI Master/Slave interfaces.

Assign interrupts if required.

Validate the design (Ctrl + Shift + V) to ensure there are no connection errors.

⚡ Step 4: Clock & Reset Configuration

Ensure a 100 MHz PL clock (FCLK0) is connected to the AXI interconnects.

Route reset signals properly to all AXI IPs.

Optionally, use a Clock Wizard for custom frequency generation.

🧠 Step 5: Address Mapping

Open Address Editor → verify memory maps for all connected AXI slaves.

Ensure each IP has a unique address range.

Modify addresses if there’s an overlap.

🧩 Step 6: Validate and Generate Bitstream

Run Validate Design (no errors or critical warnings should remain).

Click Generate Bitstream.

Once complete, export the hardware description:

File → Export → Export Hardware → Include bitstream


Save as:

embd_sys_wrapper.xsa


Example path:

/home/vishwajeet/Documents/xsa/embd_sys_wrapper.xsa

🚀 Step 7: Import to PetaLinux

After exporting the .xsa, link it to your PetaLinux project:

petalinux-config --get-hw-description=/home/vishwajeet/Documents/xsa/


This integrates your Vivado hardware platform (PS + IPs) into the embedded Linux environment.
<img width="1920" height="1080" alt="Screenshot from 2025-11-06 17-05-12" src="https://github.com/user-attachments/assets/b0553c4d-1c71-4c91-b425-d48359377511" />
<img width="1920" height="1080" alt="Screenshot from 2025-11-06 17-05-02" src="https://github.com/user-attachments/assets/26a189c2-2700-4e5d-b136-ec4e17175067" />



# -PetaLinux-Python-Integration-on-Zynq-UltraScale-ZCU104-
 This project demonstrates how to integrate Python 3 and its core modules into a custom PetaLinux build using a hardware design exported from Vivado as an .xsa file. It provides a lightweight embedded Linux environment for running Python-based AI/ML or control applications directly on the Zynq UltraScale+ MPSoC platform
📘 Project Overview

This project demonstrates how to integrate Python 3 and its core modules into a custom PetaLinux build using a hardware design exported from Vivado as an .xsa file.
It provides a lightweight embedded Linux environment for running Python-based AI/ML or control applications directly on the Zynq UltraScale+ MPSoC platform (e.g., ZCU104 board).

🧩 Key Features

✅ Uses hardware description from Vivado (.xsa file)

✅ Custom PetaLinux build for ZynqMP architecture

✅ Integrated Python 3 runtime and modules

✅ Supports execution of custom Python scripts at boot

✅ Can be extended to run AI/ML inference, data logging, or control tasks

✅ Compatible with Vitis AI for acceleration support

⚙️ Project Setup
1. Hardware Description Import
petalinux-create -t project --name python_proj
cd python_proj
petalinux-config --get-hw-description=/home/vishwajeet/Documents/xsa/

2. Enable Python in Root Filesystem

From the menu:

petalinux-config -c rootfs


Then navigate to:

Filesystem Packages → Languages → Python


Enable:

[ * ] python3

[ * ] python3-modules

(Optional) other modules such as python3-math, python3-json, python3-datetime

3. Build the Image
petalinux-build

4. Package Boot Files
petalinux-package --boot --fsbl images/linux/zynqmp_fsbl.elf \
--u-boot --fpga images/linux/system.bit

🧠 Adding Custom Python Applications

To include your own Python scripts in the image:

Place them under:

project-spec/meta-user/recipes-apps/python-app/files/


Create a recipe (python-app.bb):

SUMMARY = "Custom Python App"
LICENSE = "CLOSED"
SRC_URI = "file://my_script.py"

do_install() {
    install -d ${D}${bindir}
    install -m 0755 ${WORKDIR}/my_script.py ${D}${bindir}/my_script.py
}


Add this recipe to your root filesystem:

petalinux-config -c rootfs


→ enable your app under User Packages.

Rebuild:

petalinux-build


Now your script will be available in /usr/bin/ on the board.

💻 Target Board<img width="1920" height="1080" alt="Screenshot from 2025-11-06 16-39-01" src="https://github.com/user-attachments/assets/3d3855a0-0b5c-469e-a277-1e22975eaeae" />
<img width="1920" height="1080" alt="Screenshot from 2025-11-06 16-38-56" src="https://github.com/user-attachments/assets/f4248005-b975-4cff-88a8-173e849b2b58" />
<img width="781" height="724" alt="python_petralinux" src="https://github.com/user-attachments/assets/776f24e8-8b47-4c59-ab67-83ef8de912c9" />
<img width="1045" height="402" alt="petalinux" src="https://github.com/user-attachments/assets/4ded834d-7c69-4847-b8b2-c1dfe86c045f" />
<img width="1151" height="774" alt="packageupdation" src="https://github.com/user-attachments/assets/fe27ad8f-0528-4d14-b7a6-4211e79b54de" />


Board: Zynq UltraScale+ ZCU104

Processor: ARM Cortex-A53 Quad-Core

Linux Kernel: Petalinux 2023.2

Language: Python 3.x

🚀 Future Enhancements

Add AI inference support via Vitis AI runtime

Implement MQTT or socket-based data exchange

Integrate with FPGA accelerators through PYNQ overlays

Develop GUI or web dashboard for embedded control
