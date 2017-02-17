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
| `timezone` | `America/Los_Angeles` | Self Explanatory |
| `jboss_eap_golden_image_dir` | `/mnt/nfs/ansible/redhat/rh_jboss_golden_images` | Directory location of golden image zip |
| `jboss_eap_golden_image_subdir` | `jboss-eap-7.0` | Directory name of root directory inside zip |
| `jboss_eap_golden_image_name` | `jboss-eap-7.0.0` | The name of the zip file (excluding .zip)|
| `jboss_serverid` | `jbosspc-{{ instance.name }}` |  Serverid |
| `jboss_eap_base_dir` | `/jboss` |  Base Directory for EAP Installation |
| `jboss_eap_instance_name` | `default` |  Name of the separate running Red Hat JBoss EAP instance |
| `jboss_eap_instance_standalone_file` | `standalone-full-ha.xml` | Name of the used standalone XML file |
| `jboss_eap_instance_service_name` | `jboss_{{ jboss_eap_instance_name }}` | JBoss EAP service name|
| jboss_app_users:<br>     user: jbossapp<br>  group: jbossapp<br>  user_home: "/home/jbossapp" | | System user configuration |
| jboss_management_users:<br> - user: test<br> password: test<br> - user: test<br> password: test| | JBoss Management users [Set this in vault_files] |
| `jboss_bind_address` | `"0.0.0.0"` | JBoss EAP IP Address to bind to |
| `jboss_eap_bind_ip_address_management` | `"0.0.0.0"` | JBoss EAP IP Address to bind to for management |
| `jvm_xm` | `512` | alue for the xms and xmx (both are set equal) |
| `jboss_eap_max_post_size` | `157286400` | Max POST Size|
| `jboss_mod_cluster_proxies` | `proxyjbossweb{{ jboss_eap_instance_name }}` | Mod_cluster proxies name |
| `jboss_outbound_socket_name` | `proxyjbossweb{{ jboss_eap_instance_name }}` | Outbound socket name |
| jboss_management_users: <br> - user: admin1 <br>   password: "12345#%" <br> - user: admin2 <br>   password: "123456" | | List of administrator user accounts and passwords to configure |
| `java_pkg_name` | `java-1.8.0-openjdk-devel` | Used java version: Java 8 JDK.  |
| `jboss_java_home` | `/usr/lib/jvm/java-1.8.0-openjdk` | Default JAVA_HOME |
| `JAVA_OPTS` |  `-Djava.net.preferIPv4Stack=true -Xms8224m -Xmx8224m -XX:PermSize=526m -Dsun.rmi.dgc.client.gcInterval=3600000 -Dsun.rmi.dgc.server.gcInterval=3600000 -Dorg.jboss.resolver.warning=true -server -Dgw.server.mode=dev -Djboss.as.management.blocking.timeout=1200` | Java params |
| `jboss_eap_jgroups_multicast_address` | `230.0.0.4` | Default Address  |
| `jboss_eap_modcluster_multicast_address` | `224.0.1.105` | Default Address |
| `user_limits` | `See Defaults/main.yml` | Default User Limits |
| `jboss_eap_war_files` | `[ /path/to/war.war, /path/to/war2/war` | (Empty )List with paths to War files to be installed |
| `jboss_datasource` | `See Defaults/main.yml` | Datasource  |
| `jboss_jndi_name` | `java:jboss/datasources/pcDataSource` | JNDI Name |
| `jboss_jdbc_driver` | `See Defaults/main.yml` | JDBC Configuration |

*Ports Variables*

| Name              | Default Value       | Description          |
|-------------------|---------------------|----------------------|
| `jboss_eap_instance_port_offset` | `0` | Port offset for the JBoss EAP instance  |
| `jboss_eap_instance_management_http_port` | `9990` | Default Management Port |
| `jboss_eap_instance_management_https_port` | `9993` | Default Management SSL Port |
| `jboss_eap_instance_ajp_port` | `8009` | AJP Port |
| `jboss_eap_instance_http_port` | `8080` | HTTP Port |
| `jboss_eap_instance_https_port` | `8443` | HTTPS Port |
| `jboss_eap_instance_iiop_port` | `3528` | IIOP Port |
| `jboss_eap_instance_iiop__ssl_port` | `3529` | IIOP SSL Port |
| `jboss_eap_jgroups_mping_port` | `7600` | JGroups Ping port |
| `jboss_eap_jgroups_tcp_port` | `7600` | Port |
| `jboss_eap_jgroups_tcp_fd_port` | `57600` | Port |
| `jboss_eap_jgroups_udp_port` | `55200` | Port |
| `jboss_eap_jgroups_udp_fd_port` | `54200` | Port |
| `jboss_eap_txn_recovery_environment_port` | `4712` | Port |
| `jboss_eap_txn_status_manager_port` | `4713` | Port |
| `jboss_eap_mail_smtp_port` | `25` | Port |
| `jboss_eap_jgroups_multicast_tcp_port` | `45700` | Port |
| `jboss_eap_jgroups_multicast_udp_port` | `45688` | Port |
| `jboss_eap_modcluster_multicast_port` | `23364` | Port |
| `jboss_eap_outbound_proxy_port` | `6666` | Port |


Example Playbook
----------------

Here is a playbook creating three JBoss EAP instances on every host in "jboss-group":

```yaml
  - hosts: "jboss-group"
    vars:
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
        jboss_eap_bind_ip_address_management: "{{ ip_address }}"
      }
```


vault_files/informatica.yml should contain the following structure:
```yaml
jboss_management_users:
- user: admin1
  password: "adminpassword"
- user: admin2
  password: "adminpass"
jboss_jdbc_driver:
  name: sqljdbc-4.1.jar
  module_name: com.microsoft
  driver_class: com.microsoft.sqlserver.jdbc.SQLServerDriver
  major_version: 0
  minor_version: 0
  connection_url: "jdbc:sqlserver://127.0.0.1:1433;DatabaseName=DBA1"
  username: user
  password: password
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

Original Credit to:
* [Marc Zottner](https://github.com/Maarc)
* [Roeland van de Pol](https://github.com/roelandpol)

Updated by

* Tommy
* [Matt Margolin](https://github.com/mm0)
