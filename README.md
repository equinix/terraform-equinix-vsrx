# Equinix Network Edge: Juniper Networks vSRX firewall

A Terraform module to create Juniper Networks vSRX firewall network device
on the Equinix platform.

![Terraform status](https://github.com/equinix/terraform-equinix-vsrx/workflows/Terraform/badge.svg)
![License](https://img.shields.io/github/license/equinix/terraform-equinix-vsrx)

Supported device modes:

| Management Mode | License mode | Notes |
|-----------------|--------------|-------|
| Equinix managed | Subscription |-|
| Equinix managed | Bring your own license | `license_file` required |
| Self managed    | Bring your own license | `license_file` required |

## Requirements

| Name | Version |
|------|---------|
| terraform | >= 0.13.0 |
| equinix/equinix | >= 1.1.0 |

## Providers

| Name | Version |
|---------|----------|
| equinix/equinix | >= 1.1.0 |

## Assumptions

* if `account_number` is not provided, then `Active` account within given metro
will be used
* most recent, stable version of a device software for a given `software_package`
will be used
* secondary device name will be same as primary with `-secondary` suffix added
* secondary device notification list will be same as for primary
* secondary device `ssh-key` will be same as for primary (if provided)

## Example usage

```hcl
provider equinix {
  client_id     = var.equinix_client_id
  client_secret = var.equinix_client_secret
}

module "vsrx" {
  source           = "equinix/vsrx/equinix"
  self_managed     = true
  byol             = true
  license_file     = "/tmp/jnpr-pri.lic"
  metro_code       = "SV"
  platform         = "small"
  software_package = "STD"
  name             = "tf-tst-vsrx"
  hostname         = "pri"
  term_length      = 1
  notifications    = ["test@test.com"]
  ssh_key = {
    username = "john"
    key_name = equinix_network_ssh_key.john.name
  }
  secondary = {
    enabled      = true
    license_file = "/tmp/jnpr-sec.lic"
    metro_code   = "DC"
    hostname     = "sec"
  }
}
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
|metro_code|Two-letter device location's metro code|`string`|`""`|yes|
|account_number|Billing account number for a device. If not provided, active account for a device metro code will be used|`string`|`0`|no|
|platform|Device hardware platform flavor: `small`, `medium`, `large`|`string`|`""`|yes|
|software_package|Device software package: `STD`|`string`|`""`|yes|
|self_managed|Determines device management mode: self-managed or Equinix managed|`bool`|`false`|no|
|byol|Determines device licensing mode: bring your own license or subscription|`bool`|`false`|no|
|license_file|Path to license file required for BYOL licensing mode|`string`|`""`|no|
|name|Device name|`string`|`""`|yes|
|hostname|Device hostname prefix|`string`|`""`|yes|
|term_length|Term length in months: `1`, `12`, `24`, `36`|`number`|`0`|yes|
|notifications|List of email addresses that will receive notifications about device|`list(string)`|n/a|yes|
|acl_template_id|Identifier of a network ACL template that will be applied on a device|`string`|`""`|no|
|additional_bandwidth|Amount of additional internet bandwidth for a device, in Mbps|`number`|`0`|no|
|ssh_key|Map of SSH public key attributes|`map`|N/A|no|
|secondary|Map of secondary device attributes in redundant setup|`map`|N/A|no|

SSH key map attributes:

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
|username|Username associated with a given public key|`string`|`""`|yes|
|key_name|The name of the SSH public key|`string`|`""`|yes|

Secondary device map attributes:

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
|enabled|Value that determines if secondary device shall be created|`bool`|`false`|no|
|license_file|Path to license file required for BYOL licensing mode|`string`|`""`|no|
|metro_code|Two-letter secondary device location's metro code|`string`|`""`|yes|
|account_number|Billing account number for a device. If not provided, active account for a device metro code will be used|`string`|`0`|no|
|hostname|Device hostname prefix|`string`|`""`|yes|
|acl_template_id|Identifier of a network ACL template that will be applied on a secondary device|`string`|`""`|no|
|additional_bandwidth|Amount of additional internet bandwidth for a secondary device, in Mbps|`number`|`0`|no|

## Outputs

| Name | Description |
|------|-------------|
|id|Device identifier|
|status|Device provisioning status|
|license_status|Device license status|
|account_number|Device billing account number|
|cpu_count|Number of device CPU cores|
|memory|Amount of device memory|
|software_version|Device software version|
|region|Device region|
|ibx|Device IBX center code|
|ssh_ip_address|Device SSH interface IP address|
|ssh_ip_fqdn|Device SSH interface FQDN|
|interfaces|List of network interfaces present on a device|
|secondary|Secondary device outputs (same as for primary). Present when secondary device was enabled|
