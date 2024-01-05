
# Proxmox NVIDIA Tesla M10 GPU Passthrough Setup with CUDA Installation

This README documents the process of setting up an NVIDIA Tesla M10 GPU for passthrough in a Proxmox VE (Virtual Environment) and installing the CUDA Toolkit.

## Overview

1. **BIOS Configuration**
2. **Proxmox Configuration**
3. **Installing NVIDIA Drivers**
4. **Configuring VM for GPU Passthrough**
5. **CUDA Toolkit Installation**
6. **Post-Installation Actions**

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

## Conclusion

This setup enables the use of an NVIDIA Tesla M10 GPU in a Proxmox VE for direct passthrough to VMs. Additionally, it includes the installation of the CUDA Toolkit and necessary post-installation steps. For detailed issues or specific configurations, consult the Proxmox forums or NVIDIA's support resources.
