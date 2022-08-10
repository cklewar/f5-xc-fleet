# F5-XC-DC-FLEET

This repository consists of Terraform templates to create a F5XC DC Fleet.

## Usage

- Clone this repo with: `git clone --recurse-submodules https://github.com/cklewar/f5-xc-fleet`
- Enter repository directory with: `cd f5-xc-fleet`
- Obtain F5XC API certificate file from Console and save it to `cert` directory
- Pick and choose from below examples and add mandatory input data and copy data into file `main.tf.example`.
- Rename file __main.tf.example__ to __main.tf__ with: `rename main.tf.example main.tf`
- Initialize with: `terraform init`
- Apply with: `terraform apply -auto-approve` or destroy with: `terraform destroy -auto-approve`

### Example Output

```bash
Terraform will perform the following actions:

  # module.fleet.volterra_fleet.fleet will be created
  + resource "volterra_fleet" "fleet" {
      + default_storage_class                = true
      + deny_all_usb                         = true
      + disable_gpu                          = true
      + disable_vm                           = true
      + enable_default_fleet_config_download = true
      + fleet_label                          = "f5xc-fleet-01"
      + id                                   = (known after apply)
      + logs_streaming_disabled              = true
      + name                                 = "f5xc-fleet-01"
      + namespace                            = "system"
      + no_bond_devices                      = true
      + no_dc_cluster_group                  = true
      + no_storage_device                    = true
      + no_storage_interfaces                = true
      + no_storage_static_routes             = true

      + inside_virtual_network {
          + kind      = (known after apply)
          + name      = "f5xc-vn-inside-01"
          + namespace = "system"
          + tenant    = "***"
        }

      + interface_list {
          + interfaces {
              + name      = "f5xc-fleet-interface-01"
              + namespace = "system"
              + tenant    = "***"
            }
        }

      + outside_virtual_network {
          + kind      = (known after apply)
          + name      = "f5xc-vn-outside-01"
          + namespace = "system"
          + tenant    = "***"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

## Create F5XC Fleet

```hcl
variable "project_prefix" {
  type        = string
  description = "prefix string put in front of string"
  default     = "f5xc"
}

variable "project_suffix" {
  type        = string
  description = "prefix string put at the end of string"
  default     = "01"
}

variable "f5xc_api_p12_file" {
  type = string
}

variable "f5xc_api_url" {
  type = string
}

variable "f5xc_tenant" {
  type = string
}

module "fleet" {
  source                       = "./modules/f5xc/fleet"
  f5xc_fleet_name              = format("%s-fleet-%s", var.project_prefix, var.project_suffix)
  f5xc_fleet_label             = format("%s-fleet-%s", var.project_prefix, var.project_suffix)
  f5xc_outside_virtual_network = [format("%s-vn-outside-%s", var.project_prefix, var.project_suffix)]
  f5xc_inside_virtual_network  = [format("%s-vn-inside-%s", var.project_prefix, var.project_suffix)]
  f5xc_networks_interface_list = [format("%s-fleet-interface-%s", var.project_prefix, var.project_suffix)]
  f5xc_namespace               = "system"
  f5xc_tenant                  = var.f5xc_tenant
  f5xc_api_url                 = var.f5xc_api_url
  f5xc_api_p12_file            = var.f5xc_api_p12_file
}
```