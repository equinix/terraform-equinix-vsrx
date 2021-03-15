# Equinix Network Edge example: Juniper Networks vSRX firewall

This example shows how to create redundant Juniper Networks vSRX firewall devices
on Platform Equinix using Equinix vSRX Terraform module and Equinix Terraform provider.

In addition to pair of vSRX devices, following resources are being created
in this example:

* SSH public key that will be configured on both devices
* two ACL templates, one for each of the device

The devices are created in self managed mode with bring your own license (BYOL),
Remaining parameters include:

* small hardware platform (2CPU cores, 4GB of memory)
* STD software package
* 100 Mbps of additional internet bandwidth on each device
