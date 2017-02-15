mm0.rh-jboss-common 
===

[![Build Status](https://travis-ci.org/mm0/ansible-role-redhat-jboss-eap.svg?branch=master)](https://travis-ci.org/mm0/ansible-role-redhat-jboss-eap) [![Galaxy](https://img.shields.io/badge/galaxy-mmo.rh--jboss--eap-blue.svg?style=flat)](https://galaxy.ansible.com/mm0/rh-jboss-eap)


Description
-----------

Advanced Ansible role that manages [Red Hat JBoss Enterprise Application Platform (EAP) 6 or 7](https://access.redhat.com/documentation/en/red-hat-jboss-enterprise-application-platform/) instances.

Core implemented features in this role:

- multiple parallel versions and profile support
- multiple Red Hat JBoss EAP instances per host
- graceful orchestration and shutdown (prerequisite for rolling updates)

Please have a look at [this example](https://github.com/mm0/ansible_middleware_soe) showing how to easily operate Red Hat JBoss middleware products using this role.


Requirements
------------

This role has been tested on Ansible 2.0.2.0 and 2.1.1.0. It requires Red Hat Enterprise Linux 7.


Dependencies
------------

 None

Installation
------------

    ansible-galaxy install mm0.rh-jboss-eap -p roles


Role Variables
--------------

*General variables*

| Name              | Default Value       | Description          |
|-------------------|---------------------|----------------------|
| `download_dir` | `/tmp` | Directory containing all required middleware binaries on the managed remote host. Mandatory |
| `jboss.user` | `jboss` | Linux user name used for running EAP |
| `jboss.group` | `jboss` | Linux group name used for the `jboss.user` |
| `jboss.group_id` | `500` | Linux group id taken for `jboss.group` |
| `jboss.user_home` | `/opt/jboss` | Linux home directory for `jboss.user`  |


*Instance specific variables*

| Name              | Default Value       | Description          |
|-------------------|---------------------|----------------------|
| `jboss_eap_instance_name` | `default` | Name of the separate running Red Hat JBoss EAP instance. Mandatory |
| `jboss_eap_instance_admin_user` | `redhat` | Red Hat JBoss EAP admin user name. Mandatory |
| `jboss_eap_instance_admin_password` | `ba2caa9378fa898f1dea88804abe52b4` | Red Hat JBoss EAP admin password ("redhat123!") hashed according to HEX( MD5( username ':' realm ':' password)). Mandatory |
| `jboss_eap_instance_admin_groups` | empty | Red Hat JBoss EAP admin user groups |
| `jboss_eap_golden_image_name` | empty | Name of the used Red Hat JBoss EAP golden image. Mandatory |
| `jvm_xm` | `512` | Value for the xms and xmx (both are set equal) in MB  |
| `jboss_eap_instance_port_offset` | `0` | Port offset for the JBoss EAP instance  |
| `jboss_eap_instance_cli_used_default_port` | `9999` | Default port for the native management interface |
| `jboss_eap_instance_cli_default_port` | `8888` | Port used only during updates using the CLI (port should be available) |
| `jboss_eap_instance_standalone_file` | `standalone.xml` | Name of the used standalone XML file |


Example Playbook
----------------

Here is a playbook creating three JBoss EAP instances on every host in "jboss-group":

```yaml
  - hosts: "jboss-group"
    vars:
    - golden_image_name: "jboss-eap-7.0.0"
    - instance:
      name: "dev1"
      port_offset: "10"
    - ip_address: "{{ ansible_ens160.ipv4.address }}"
    vars_files:
    - vault_files/jboss_management.yml
    roles:
    - {
        role: "mm0.rh-jboss-eap",
        jboss_eap_instance_name: "{{ instance.name }}",
        jboss_eap_instance_port_offset: "{{ instance.port_offset }}",
        jboss_bind_address: "{{ ip_address }}",
        jboss_eap_bind_ip_address_public: "{{ ip_address }}",
        jboss_eap_bind_ip_address_management: "{{ ip_address }}"
      }
```


Structure
---------
- Redhat-jboss-common role creates and configures jbossapp user, group and home directory for the JBoss instance.

- `defaults/main.yml` centralize the default variables that could be overridden
- `tasks/main.yml` coordinate the execution of the different tasks
- `tasks/00__prereqs.yml` Create users and groups and ensure zip installation archive is present
- `tasks/01__graceful_removal.yml` Stop any existing server with same configuration and remove its files
- `tasks/02__copy_and_unpack.yml` Create directory for the installation, unpack it
- `tasks/03__configure.yml` Configure the installation via configuration templates and set up management users
- `tasks/04__register_service.yml` register the instance as a linux service and start it (`systemctl start jboss_{{ instance.name }}`)


License
-------

[Apache 2.0](./LICENSE)


Author Information
------------------

* [Marc Zottner](https://github.com/Maarc)
* [Roeland van de Pol](https://github.com/roelandpol)
* [Matt Margolin](https://github.com/mm0)
