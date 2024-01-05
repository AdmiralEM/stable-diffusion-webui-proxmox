
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

- **Identify Correct Driver:**
  - Visit [NVIDIA Driver Downloads](https://www.nvidia.com/Download/index.aspx).
  - Select "Tesla" and "M10", along with your OS details.

- **Install Drivers:**
  - Download and install the recommended drivers.

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

