Ansible role: "Red Hat JBoss EAP" [![Build Status](https://travis-ci.org/Maarc/ansible-role-redhat-jboss-eap.svg?branch=master)](https://travis-ci.org/Maarc/ansible-role-redhat-jboss-eap)
=================================


Description
-----------

Advanced Ansible role that installs and manages instances of [Red Hat JBoss Enterprise Application Platform (EAP) 6 or 7](https://access.redhat.com/documentation/en/red-hat-jboss-enterprise-application-platform/).

Core implemented features in this role:

- multiple parallel versions and profile support
- multiple Red Hat JBoss EAP instances per host
- graceful orchestration and shutdown (prerequisite for rolling updates)
- application deployment using nexus or local files
- configuration of the Red Hat JBoss EAP instances using the CLI

Please have a look at [this example](https://github.com/Maarc/ansible_middleware_soe) showing how to easily operate Red Hat JBoss middleware products using this role.


Requirements
------------

This role has been tested on Ansible 2.0.2.0 and 2.1.1.0. It requires Red Hat Enterprise Linux 7.


Dependencies
------------

The "rh-jboss-common" role is required. It could be imported as follows:

    ansible-galaxy install -r requirements.yml -p roles


Installation
------------

    ansible-galaxy install Maarc.rh-jboss-eap


Role Variables
--------------

| Name              | Default Value       | Description          |
|-------------------|---------------------|----------------------|
| `jboss_eap_instance_name` | `default` | (mandatory) Name of the separate running Red Hat JBoss EAP instance. |
| `jboss_eap_golden_image_name` | `` | (mandatory) Name of the used Red Hat JBoss EAP golden image. |
| `download_dir` | `/tmp` | Directory containing all downloaded middleware  on the managed remote host. |



# System user configuration
jboss:
  user: jboss
  group: jboss
  group_id: 500
  user_home: "/opt/jboss"

# Value for the xms and xmx (both are set equal)
jvm_xm: 512

# Name of the separate JBoss EAP instance
jboss_eap_instance_name: default

# Port offset for the JBoss EAP instance
jboss_eap_instance_port_offset: 0

# Port used only during updates using the CLI (port should be available)
jboss_eap_instance_cli_default_port: 8888

# Default value for the used standane xml file
jboss_eap_instance_standalone_file: standalone.xml

# Application lists
app_list: { }
app_mvn_list: { }

# List of CLI files to be used for the configuration.
cli_list: { }






Example Playbook
----------------

Here is a playbook creating three JBoss EAP instances on every host in "jboss-group":

    - hosts: "jboss-group"
      roles:
        - {
            # JBoss EAP 7 instance for the ticket-monster application
            role: "rh-jboss-eap",
            jboss_eap_golden_image_name: "jboss-eap-6.4.8_GI",
            jboss_eap_instance_name: "ticket_monster",
            jboss_eap_instance_standalone_file: "standalone-full-ha.xml",
            jboss_eap_instance_port_offset: 0,
            app_list: { "ticket-monster.war" },
            cli_list: { "add_datasource.cli", "add_mod_cluster_6.cli"},
          }
        - {
            # JBoss EAP 6 instance for the petclinic application (note: toggle role and app to deploy from Nexus)
            role: "rh-jboss-eap",
            jboss_eap_golden_image_name: "jboss-eap-7.0.1_GI",
            jboss_eap_instance_name: "petclinic",
            jboss_eap_instance_standalone_file: "standalone-full-ha.xml",
            jboss_eap_instance_port_offset: "1000",
            app_list: { "petclinic.war" },
            cli_list: { "add_datasource.cli", "add_mod_cluster_7.cli"},
            #app_mvn_list: [ { g: "com.redhat.jboss", a: "petclinic.war", v: "1.0", e: "war" } ],
          }
        - {
            # JBoss EAP 7 instance for the jenkins application
            role: "rh-jboss-eap",
            jboss_eap_golden_image_name: "jboss-eap-7.0.1_GI",
            jboss_eap_instance_name: "jenkins",
            jboss_eap_instance_standalone_file: "standalone-full-ha.xml",
            jboss_eap_instance_port_offset: 2000,
            app_list: { "jenkins.war" },
            cli_list: { "add_datasource.cli", "add_mod_cluster_7.cli"},
          }


Structure
---------

- `tasks/main.yml` coordinates the execution of the different tasks
- `tasks/00__prepare.yml` does some checks and creates a
- `tasks/01__copy_and_unpack.yml`
- `tasks/02__configure.yml`
- `tasks/03__graceful_removal.yml`
- `tasks/05__register_service.yml`


License
-------

[Apache 2.0](./LICENSE)


Author Information
------------------

* [Marc Zottner](https://github.com/Maarc)
* [Roeland van de Pol](https://github.com/roelandpol)
