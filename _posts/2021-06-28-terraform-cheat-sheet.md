---
layout: post
title: "Terraform Cheat Sheet"
author: alfred
categories: [terraform, cheat sheet]
featured: false
hidden: false
toc: true
---

Terraform is HashiCorp’s infrastructure as code tool. It lets you define resources and infrastructure in human-readable, declarative configuration files, and manages your infrastructure’s lifecycle.

Infrastructure as code (IaC) tools allow you to manage infrastructure with configuration files rather than through a graphical user interface. IaC allows you to build, change, and manage your infrastructure in a safe, consistent, and repeatable way by defining resource configurations that you can version, reuse, and share.

### References

[Get Started - Azure](https://learn.hashicorp.com/collections/terraform/azure-get-started)

<!--more-->

### Commands

```sh
# Login to Azure using Azure CLI
az Login

# Initialize terraform in the configuration directory for a new configuration
terraform init

# Reconcile the state Terraform knows about (via its state file) with the real-world infrastructure
terraform refresh

# Generate an execution plan
terraform plan

# Apply the configuration changes
terraform apply

# Inspect current configuration state
terraform show

# Update configuration using a saved plan
terraform plan -out=newplan
terraform apply "newplan"

# Destroy resources
terraform plan -destroy
terraform destroy

# Apply configuration using variable files
$ terraform apply \
-var-file='common-vars.tf' \
-var-file='create-vm-vars.tf'
```

### Common Variables

```tf
location = "westus"
prefix = "tf"
admin_username = "plankton"
admin_password = "Password1234!"
```

### Example VM Creation - Variables

```tf
variable "location" {}

variable "admin_username" {
  type        = "string"
  description = "Administrator user name for virtual machine"
}

variable "admin_password" {
  type        = "string"
  description = "Password must meet Azure complexity requirements"
}

variable "prefix" {
  type    = "string"
  default = "my"
}

variable "tags" {
  type = "map"

  default = {
    Environment = "Terraform GS"
    Dept        = "Engineering"
  }
}

variable "sku" {
  default = {
    westus = "16.04-LTS"
    eastus = "18.04-LTS"
  }
}
```

### Example VM Creation

```tf
# Configure the Microsoft Azure Provider.
provider "azurerm" {
    version = ">= 1.32"
}

# Create a resource group
resource "azurerm_resource_group" "rg" {
    name     = "${var.prefix}TFRG"
    location = var.location
    tags     = var.tags
}

# Create virtual network
resource "azurerm_virtual_network" "vnet" {
    name                = "${var.prefix}TFVnet"
    address_space       = ["10.0.0.0/16"]
    location            = var.location
    resource_group_name = azurerm_resource_group.rg.name
    tags                = var.tags
}

# Create subnet
resource "azurerm_subnet" "subnet" {
    name                 = "${var.prefix}TFSubnet"
    resource_group_name  = azurerm_resource_group.rg.name
    virtual_network_name = azurerm_virtual_network.vnet.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IP
resource "azurerm_public_ip" "publicip" {
    name                         = "${var.prefix}TFPublicIP"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.rg.name
    allocation_method            = "Dynamic"
    tags                         = var.tags
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "nsg" {
    name                = "${var.prefix}TFNSG"
    location            = var.location
    resource_group_name = azurerm_resource_group.rg.name
    tags                = var.tags

    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }
}

# Create network interface
resource "azurerm_network_interface" "nic" {
    name                      = "${var.prefix}NIC"
    location                  = var.location
    resource_group_name       = azurerm_resource_group.rg.name
    network_security_group_id = azurerm_network_security_group.nsg.id
    tags                      = var.tags

    ip_configuration {
        name                          = "${var.prefix}NICConfg"
        subnet_id                     = azurerm_subnet.subnet.id
        private_ip_address_allocation  = "dynamic"
        public_ip_address_id          = azurerm_public_ip.publicip.id
    }
}

# Create a Linux virtual machine
resource "azurerm_virtual_machine" "vm" {
    name                  = "${var.prefix}TFVM"
    location              = var.location
    resource_group_name   = azurerm_resource_group.rg.name
    network_interface_ids = [azurerm_network_interface.nic.id]
    vm_size               = "Standard_DS1_v2"
    tags                  = var.tags

    storage_os_disk {
        name              = "${var.prefix}OsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = lookup(var.sku, var.location)
        version   = "latest"
    }

    os_profile {
        computer_name  = "${var.prefix}TFVM"
        admin_username = var.admin_username
        admin_password = var.admin_password
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

}

output "ip" {
    value = azurerm_public_ip.publicip.ip_address
}

output "os_sku" {
    value = lookup(var.sku, var.location)
}
```
