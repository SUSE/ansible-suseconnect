SUSEConnect
===========

![Ansible Lint](https://github.com/HVSharma12/suseconnect/actions/workflows/ansible-lint.yml/badge.svg?branch=main)

This Ansible role is used to manage SUSE Linux system registrations with the SUSE Customer Center (SCC) or a local Subscription Management Tool (SMT) server. It automates the process of registering and deregistering systems, as well as managing additional products and modules on a SUSE system

This role includes:

  - Registration of a SUSE system to SCC/SMT.
  - Activation of specific add-on products or modules.
  - Deregistration of systems or products.
  - Precheck tasks to ensure a smooth registration process.
  - Removal of subscriptions not listed in the registration configuration.

Requirements
------------

Before using this role, ensure that you have the following:

  - A valid registration key for your SUSE products, which can be obtained with your SUSE subscription.
  - For some products, additional registration keys are required.
  - You need to know the internal product names for the products you're registering. These names can be found in [PRODUCTS.md](PRODUCTS.md).

Role Variables
--------------

The following variables can be configured when using this role:

| Variable                            | Type   | Description                                                                                 |
|-------------------------------------|--------|---------------------------------------------------------------------------------------------|
| `suseconnect_base_product:`         | list   | List of products that should be activated on the target system.                             |
| `suseconnect_subscriptions:`        | list   | List of additional modules or products to be registered.                                    |
| `  - product:`                      | string | Internal product name, see [PRODUCTS.md](PRODUCTS.md) for a list.                           |
| `    version:`                      | string | Version of the product to be activated, defaults to the base OS version.                    |
| `    arch:`                         | string | Architecture of the product, defaults to the OS architecture (ansible_machine).(Optional)   |
| `    key:`                          | string | Additional registration key if required by the product.                                     |
| `    email:`                        | string | Email address used in SCC for registration.(Optional)                                       |
| `suseconnect_reregister:`           | bool   | Whether to force re-registration of all products, regardless of current status.             |
| `suseconnect_remove_subscriptions:` | bool   | Remove currently registered products that are not listed in suseconnect_products.           |
| `suseconnect_deregister:`           | bool   | Whether to deregister the system (default: false).                                          |

Example Task
------------

### Registering a SUSE Linux System
This example registers a SLES system and activates several modules:

```yaml
- name: Register with SCC and activate modules
  hosts: all
  become: true
  gather_facts: true
  vars:
    suseconnect_base_product:
      - product: 'SLES'
        version: '{{ ansible_distribution_version }}'
        key: '{{ sles_registration_key }}'
        arch: '{{ ansible_machine }}'

    suseconnect_subscriptions:
      - product: 'sle-module-development-tools'
        version: '{{ ansible_distribution_version }}'
      - product: 'sle-module-containers'
        version: '{{ ansible_distribution_version }}'
      - product: 'sle-module-web-scripting'
        version: '{{ ansible_distribution_version }}'
      - product: 'PackageHub'
        version: '{{ ansible_distribution_version }}'

  tasks:
    - name: Register system and modules with SUSE Customer Center
      include_role:
        name: suseconnect
```

### Deregistering Products
This example shows how to deregister products when they are no longer required on the system:

```yaml
- name: Deregister products from SCC
  hosts: all
  become: true
  vars:
    suseconnect_deregister: true

  tasks:
    - name: Deregister products from SUSE Customer Center
      include_role:
        name: suseconnect
```

### Removing Subscriptions
This task removes any subscriptions not listed in the suseconnect_products variable. It ensures the system is only registered with the necessary products, while cleaning up any unnecessary subscriptions. Below is an example where we register only the base subscription for SLES and SL-Micro systems, and remove all other subscriptions that are not listed.

```yaml
- name: Clean up old subscriptions and add base subscription to SLES and SL-Micro with basic modules
  hosts: all
  become: true
  vars:
    suseconnect_base_product:
      - product: 'SLES'
        version: '{{ ansible_distribution_version }}'
        key: '{{ sles_registration_key }}'
      - product: 'SL-Micro'
        version: '{{ ansible_distribution_version }}'
        key: '{{ sl_micro_registration_key }}'
    
    suseconnect_subscriptions:
      - product: 'sle-module-basesystem'
        version: '{{ ansible_distribution_version }}'
      - product: 'sle-module-basesystem'
        version: '{{ ansible_distribution_version }}'
    
    suseconnect_remove_subscriptions: true  # Remove subscriptions not listed above

  tasks:
    - name: Register SLES and SL-Micro with basic modules, and remove other subscriptions
      include_role:
        name: suseconnect
```

License
-------

This project is licensed under GPL-3.0.

Author Information
------------------

### Originally authored by
- Sebastian Meyer (meyer@b1-systems.de)  
  B1 Systems GmbH

### Modified and maintained by
- Harshvardhan Sharma
