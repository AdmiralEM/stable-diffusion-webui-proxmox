
# Proxmox NVIDIA Tesla M10 GPU Passthrough Setup with CUDA Installation

This README documents the process of setting up an NVIDIA Tesla M10 GPU for passthrough in a Proxmox VE (Virtual Environment) and installing the CUDA Toolkit.

## Overview

1. **BIOS Configuration**
2. **Proxmox Configuration**
3. **Installing NVIDIA Drivers**
4. **Configuring VM for GPU Passthrough**
5. **CUDA Toolkit Installation**
6. **Post-Installation Actions**
7. **Stable-Diffusion Installation**

## Detailed Steps

### BIOS Configuration

Ensure your system's BIOS is configured to support GPU passthrough:

- **Enable Virtualization:**
  - Enable `Intel VT-x` or `AMD-V` depending on your processor.
- **Enable IOMMU:**
  - Enable `Intel VT-d` or `AMD IOMMU`, as per your CPU architecture.
- **Disable Secure Boot:**
  - Secure Boot should be disabled to allow the passthrough.

### Proxmox Configuration

- **Edit GRUB Config:**
  - Modify `/etc/default/grub` to include `intel_iommu=on` or `amd_iommu=on`.
  - Update GRUB with `update-grub` and reboot.

- **Enable VFIO:**
  - Find GPU IDs with `lspci -nn | grep NVIDIA`.
  - Add to `/etc/modprobe.d/vfio.conf` with `options vfio-pci ids=xxxx:xxxx`.

- **Update Initramfs:**
  - Apply changes with `update-initramfs -u`.

### Installing NVIDIA Drivers

- **Install Kernel Headers and Development Packages:**
  ```bash
  sudo apt-get install linux-headers-$(uname -r)
  ```

- **Install CUDA Repository Public GPG Key:**
  ```bash
  distribution=$(. /etc/os-release;echo $ID$VERSION_ID | sed -e 's/\.//g')
  wget https://developer.download.nvidia.com/compute/cuda/repos/$distribution/x86_64/cuda-keyring_1.0-1_all.deb
  sudo dpkg -i cuda-keyring_1.0-1_all.deb
  ```

- **Update and Install NVIDIA Drivers:**
  ```bash
  sudo apt-get update
  sudo apt-get -y install cuda-drivers
  ```

### Configuring VM for GPU Passthrough

- **Create/Edit VM:**
  - Use Proxmox's web interface to configure your VM.
- **Add GPU to VM:**
  - Under VM Hardware settings, add the NVIDIA GPU as a PCI device.

### CUDA Toolkit Installation

- **Install CUDA Toolkit:**
  - Follow the instructions on the [CUDA Toolkit Download Page](https://developer.nvidia.com/cuda-downloads).

### Post-Installation Actions

#### Mandatory Actions

- **Environment Setup:**
  ```bash
  export PATH=/usr/local/cuda-12.2/bin${PATH:+:${PATH}}
  export LD_LIBRARY_PATH=/usr/local/cuda-12.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
  ```

#### Recommended Actions

- **Install Persistence Daemon:**
  ```bash
  /usr/bin/nvidia-persistenced --verbose
  ```

- **Verify the Installation:**
  - Compile and run sample programs from the CUDA samples repository.

#### Optional Actions

- **Install Third-party Libraries (Example for Ubuntu):**
  ```bash
  sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa-dev libfreeimage-dev libglfw3-dev
  ```

- **Select the Active Version of CUDA:**
  ```bash
  sudo update-alternatives --config cuda
  ```

## Passthrough Conclusion

This setup enables the use of an NVIDIA Tesla M10 GPU in a Proxmox VE for direct passthrough to VMs. Additionally, it includes the installation of the CUDA Toolkit and necessary post-installation steps. For detailed issues or specific configurations, consult the Proxmox forums or NVIDIA's support resources.


## Installing Stable Diffusion with NVIDIA GPU Passthrough in Proxmox VM

This section details the steps to install Stable Diffusion, a deep learning, text-to-image model, in a Proxmox VM with an NVIDIA GPU passthrough.

### Prerequisites

- A VM in Proxmox with an NVIDIA GPU passthrough setup.
- Ensure that the NVIDIA drivers are correctly installed in the VM.

### Installation Steps

1. **Set Up the VM:**
   - Create a new VM in Proxmox or use an existing one where the NVIDIA GPU has been passed through.
   - Install a compatible operating system (e.g., Ubuntu).

2. **Install Dependencies:**
   - Update the package lists and install necessary dependencies. For Ubuntu, you would typically run:
     ```bash
     sudo apt update
     sudo apt install git python3 python3-pip
     ```

3. **Clone Stable Diffusion Repository:**
   - Clone the Stable Diffusion repository from GitHub:
     ```bash
     git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
     cd stable-diffusion-webui
     ```

4. **Install Python Requirements:**
   - Inside the cloned repository, install the required Python packages:
     ```bash
     pip3 install -r requirements.txt
     ```

5. **Download the Model:**
   - Follow the instructions provided in the repository or on the [Stable Diffusion website](https://rentry.org/voldy) to download the necessary model files.
   - Place the downloaded model files in the appropriate directory as instructed.

6. **Run the Application:**
   - Execute the script to start the Stable Diffusion web UI:
     ```bash
     python3 scripts/webui.py
     ```
   - This will start a local server, typically accessible via a web browser.

7. **Accessing the Web UI:**
   - Access the web UI through your browser by navigating to the IP address of the VM followed by the port number (default is usually `http://VM_IP:PORT`).

### Post-Installation

- **Testing:** After installation, test the setup by inputting text prompts into the Stable Diffusion web UI and generating images.
- **Configuration Adjustments:** You may need to adjust configurations based on your specific requirements, such as changing the port number or setting up additional features.

## Conclusion

With these steps, you should be able to run Stable Diffusion in a Proxmox VM using an NVIDIA GPU passed through. This setup allows for efficient utilization of GPU resources for AI-based image generation.
