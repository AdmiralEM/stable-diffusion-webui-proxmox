
# Setting up Stable Diffusion Web-UI on Proxmox with GPU Passthrough

# Proxmox NVIDIA Tesla M10 GPU Passthrough Setup

This README documents the process of setting up an NVIDIA Tesla M10 GPU for passthrough in a Proxmox VE (Virtual Environment). The steps cover system preparation, BIOS settings, Proxmox configuration, and driver installation.

## Overview for Passthrough

1. **BIOS Configuration**
2. **Proxmox Configuration**
3. **Installing NVIDIA Drivers**
4. **Configuring VM for GPU Passthrough**
5. **Verification and Troubleshooting**

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
  - Modify `/etc/default/grub` to include `intel_iommu=on` (`amd_iommu=on` should already be turned on in AMD chips (need to verify)).
  - Update GRUB with `update-grub` and reboot.

- **Enable VFIO:**
  - Find GPU IDs with `lspci -nn | grep NVIDIA`.
  - Add to `/etc/modprobe.d/vfio.conf` with `options vfio-pci ids=xxxx:xxxx`.

- **Update Initramfs:**
  - Apply changes with `update-initramfs -u`.

### Installing NVIDIA Drivers

Source: https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts
The installation of the NVIDIA driver requires specific kernel headers and development packages corresponding to the running version of your kernel.

#### Prerequisites

- **Install Kernel Headers and Development Packages:**
  Ensure that the kernel headers and development packages for your current kernel version are installed. For example, if your system is running kernel version `4.4.0`, you must have the `4.4.0` kernel headers and development packages installed.

  Install them using the following command:
  ```bash
  sudo apt-get install linux-headers-$(uname -r)
  ```
 #### Installing CUDA Repository GPG Key

- **Install the CUDA Repository Public GPG Key:**
  This can be achieved through the `cuda-keyring` package or manually. The use of `apt-key` is deprecated.
  The newest for Proxmox is debian 12: https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/

  Use the following commands to install the key:
  ```bash
  distribution=$(. /etc/os-release;echo $ID$VERSION_ID | sed -e 's/\.//g')
  wget https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/cuda-keyring_1.1-1_all.deb
  sudo dpkg -i cuda-keyring_1.1-1_all.deb
  ```

#### Driver Installation

- **Update and Install NVIDIA Drivers:**
  Update the APT repository cache and install the drivers using the `cuda-drivers` meta-package. The `--no-install-recommends` option can be used for a lean installation without dependencies on X packages, which is useful for headless installations.

  ```bash
  sudo apt-get update
  sudo apt-get -y install cuda-drivers
  ```

#### Post-Installation

- **Follow Post-Installation Steps:**
  Refer to the [CUDA Installation Guide for Linux](https://developer.nvidia.com/cuda-toolkit) for setting up environment variables, configuring the NVIDIA persistence daemon (recommended), and verifying the successful installation of the driver  


### Configuring VM for GPU Passthrough

- **Create/Edit VM:**
  - Use Proxmox's web interface to configure your VM.
- **Add GPU to VM:**
  - Under VM Hardware settings, add the NVIDIA GPU as a PCI device.

### Verification and Troubleshooting

- **Verify GPU Functionality:**
  - Use `nvidia-smi` and other tools to ensure the GPU is functioning correctly in the VM.
- **Troubleshooting:**
  - Check `dmesg` and system logs for any errors related to NVIDIA or IOMMU.

## Conclusion

This setup enables the use of an NVIDIA Tesla M10 GPU in a Proxmox VE for direct passthrough to VMs. For detailed issues or specific configurations, consult the Proxmox forums or NVIDIA's support resources.

