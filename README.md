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

💻 Target Board

Board: Zynq UltraScale+ ZCU104

Processor: ARM Cortex-A53 Quad-Core

Linux Kernel: Petalinux 2023.2

Language: Python 3.x

🚀 Future Enhancements

Add AI inference support via Vitis AI runtime

Implement MQTT or socket-based data exchange

Integrate with FPGA accelerators through PYNQ overlays

Develop GUI or web dashboard for embedded control
