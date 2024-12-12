# SUSEConnect

![Ansible Lint](https://github.com/HVSharma12/ansible-suseconnect/actions/workflows/ansible-lint.yml/badge.svg?branch=main)

This Ansible role is used to manage SUSE Linux system registrations with the SUSE Customer Center (SCC) or a local Subscription Management Tool (SMT) server. It automates the process of registering and deregistering systems, as well as managing additional products and modules on a SUSE system

This role includes:

- Registration of a SUSE system to SCC/SMT.
- Activation or removal of specific add-on products or modules.
- Deregistration of systems or products.
- Precheck tasks to ensure a smooth registration process.

## Requirements

Before using this role, ensure that you have the following:

- A valid registration key for your SUSE products, which can be obtained with your SUSE subscription.
- For some products, additional registration keys are required.

## Role Variables

The following variables can be configured when using this role:

### suseconnect_base_product

- **Type**: dict
- **Description**: Defines the base product that should be activated on the target system. The dictionary should include the following keys:
  - `product`
    - **Type**: string
    - **Description**: Internal product name, see [PRODUCTS.md](PRODUCTS.md) for a list, defaults to (`ansible_distribution`)
    - **Required**: No
  - `key`
    - **Type**: string
    - **Description**: Registration key for the product.
    - **Required**: Yes
  - `version`
    - **Type**: string
    - **Description**: Version of the product to be activated, defaults to the base OS version.
    - **Required**: No
  - `arch`
    - **Type**: string
    - **Description**: Architecture of the product, defaults to the OS architecture (`ansible_machine`).
    - **Required**: No
  - `email`
    - **Type**: string
    - **Description**: Email address used in SCC for registration.
    - **Required**: No

### suseconnect_subscriptions

- **Type**: list
- **Description**: List of additional modules or products to be added or removed on the target system.

  Each item is a dictionary containing the following keys:

  - `name`
    - **Type**: string
    - **Description**: Internal product name, see [PRODUCTS.md](PRODUCTS.md) for a list.
    - **Required**: Yes
  - `state`
    - **Type**: string
    - **Description**: The state of the subscription, which can be either enabled or disabled. If not specified, the default state is enabled.
    - **Required**: No
    - **Default**: enabled
  - `version`
    - **Type**: string
    - **Description**: Version of the product to be activated, defaults to the base OS version, defaults to (`ansible_distribution_version`).
    - **Required**: No
  - `arch`
    - **Type**: string
    - **Description**: Architecture of the product, defaults to the OS architecture (`ansible_machine`).
    - **Required**: No
  - `key`
    - **Type**: string
    - **Description**: Additional registration key if required by the product.
    - **Required**: No
  - `email`
    - **Type**: string
    - **Description**: Email address used in SCC for registration.
    - **Required**: No

### suseconnect_reregister

- **Type**: bool
- **Description**: Whether to force re-registration of base product, regardless of current status (default: false).

### suseconnect_deregister

- **Type**: bool
- **Description**: Whether to deregister the system (default: false).

## Example Task

### Registering a SUSE Linux System

This example registers a SLES system and activates several modules:

```yaml
- name: Register with SCC and activate modules
  hosts: all
  vars:
    suseconnect_base_product:
      key: '{{ sles_registration_key }}'
      product: '{{ ansible_distribution }}'

    suseconnect_subscriptions:
      - {name: "sle-module-containers", state: enabled}
      - {name: "PackageHub", state: disabled}
      - {name: "sle-module-python3", state: enabled}

  tasks:
    - name: Register system and modules with SUSE Customer Center
      ansible.builtin.include_role:
        name: suseconnect
```

### Adding or deleting modules and extensions

This task adds or removes modules and extensions. It registers or derigisters the components and enables or disables their repositories.

```yaml
- name: Adding or deleting modules and extensions
  hosts: all
  vars:
    suseconnect_subscriptions:
      - {name: "sle-module-containers", state: enabled}
      - {name: "PackageHub", state: disabled}
      - {name: "sle-module-python3", state: enabled}
      - {name: "sle-ha", state: enabled, key: "REGISTRATION-KEY"}

  tasks:
    - name: Remove other subscriptions
      ansible.builtin.include_role:
        name: suseconnect
```

### Deregistering Products

This example shows how to deregister base products when they are no longer required on the system:

```yaml
- name: Deregister products from SCC
  hosts: all
  vars:
    suseconnect_deregister: true

  tasks:
    - name: Deregister products from SUSE Customer Center
      ansible.builtin.include_role:
        name: suseconnect
```

## License

This project is licensed under GPL-3.0.
