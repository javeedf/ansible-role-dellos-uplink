Uplink role
===========

This role facilitates the configuration of uplink failure detection feature attributes, and is abstracted for dellos10. It specifically enables configuration of  association between upstream and downstream interfaces known as uplink-state group.

The dellos-uplink role requires an SSH connection for connectivity to a Dell EMC Networking device. You can use any of the built-in OS connection variables .

Installation
------------

    ansible-galaxy install Dell-Networking.dellos-uplink

Role variables
--------------

- Role is abstracted using the *ansible_network_os* variable that can take dellos10 values
- If *dellos_cfg_generate* is set to true, the variable generates the role configuration commands in a file
- Any role variable with a corresponding state variable set to absent negates the configuration of that variable
- Setting an empty value for any variable negates the corresponding configuration
- Variables and values are case-sensitive

**dellos_uplink keys**

| Key        | Type                      | Description                                             | Support               |
|------------|---------------------------|---------------------------------------------------------|-----------------------|
| ``uplink_state_group`` | list | Configure the uplink state group (see ``uplink_state_group.*``) | dellos10 |
| ``uplink_state_group.id`` | integer | Configure the uplink state group instance | dellos10 |
| ``uplink_state_group.enable`` | boolean: True,False | Enable the uplink state group instance | dellos10 |
| ``uplink_state_group.uplink_type`` | list | Configure the upstream & downstream attribute (see ``uplink_type.*``) | dellos10 |
| ``uplink_type.type`` | string: upstream,downstream | Configure the uplink type | dellos10 |
| ``uplink_type.intf`` | string | Configure the uplink interface | dellos10 |
| ``uplink_type.state`` | string: absent,present\* | Removes the uplink stream if set to absent | dellos10 |
| ``uplink_state_group.state`` | string: absent,present\* | Removes the uplink state group instance if set to absent | dellos10 |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified. 

Connection variables
********************

Ansible Dell EMC Networking roles require connection information to establish communication with the nodes in your inventory. This information can exist in the Ansible *group_vars* or *host_vars* directories, or inventory or in the playbook itself.

| Key         | Required | Choices    | Description                                         |
|-------------|----------|------------|-----------------------------------------------------|
| ``ansible_host`` | yes      |            | Specifies the hostname or address for connecting to the remote device over the specified transport |
| ``ansible_port`` | no       |            | Specifies the port used to build the connection to the remote device; if value is unspecified, the ANSIBLE_REMOTE_PORT option is used; it defaults to 22 |
| ``ansible_ssh_user`` | no       |            | Specifies the username that authenticates the CLI login for the connection to the remote device; if value is unspecified, the ANSIBLE_REMOTE_USER environment variable value is used  |
| ``ansible_ssh_pass`` | no       |            | Specifies the password that authenticates the connection to the remote device |
| ``ansible_become`` | no       | yes, no\*   | Instructs the module to enter privileged mode on the remote device before sending any commands; if value is unspecified, the ANSIBLE_BECOME environment variable value is used, and the device attempts to execute all commands in non-privileged mode |
| ``ansible_become_method`` | no       | enable, sudo\*   | Instructs the module to allow the become method to be specified for handling privilege escalation; if value is unspecified, the ANSIBLE_BECOME_METHOD environment variable value is used |
| ``ansible_become_pass`` | no       |            | Specifies the password to use if required to enter privileged mode on the remote device; if ``ansible_become`` is set to no this key is not applicable |
| ``ansible_network_os`` | yes      | dellos6/dellos9/dellos10, null\*  | Loads the correct terminal and cliconf plugins to communicate with the remote device |

> **NOTE**: Asterisk (\*) denotes the default value if none is specified.

Dependencies
------------

The *dellos-uplink* role is built on modules included in the core Ansible code. These modules were added in Ansible version 2.2.0.

Example playbook
----------------

This example uses the *dellos-uplink role* to completely set the uplink sate group instance, and upstream, downstream interfaces. It creates a *hosts* file with the switch details and corresponding variables. The hosts file should define the *ansible_network_os* variable with corresponding Dell EMC networking OS name. 

When *dellos_cfg_generate* is set to true, the variable generates the configuration commands as a .part file in *build_dir* path. By default, the variable is set to false. The uplink role writes a simple playbook that only references the *dellos-uplink* role. By including the role, you automatically get access to all of the tasks to configure uplink features. 

**Sample hosts file**
 
    leaf1 ansible_host= <ip_address> 

**Sample host_vars/leaf1**

    hostname: leaf1
    ansible_become: yes
    ansible_become_method: xxxxx
    ansible_become_pass: xxxxx
    ansible_ssh_user: xxxxx
    ansible_ssh_pass: xxxxx
    ansible_network_os: dellos10
    build_dir: ../temp/dellos10
	  
    dellos_uplink:
      uplink_state_group:
        - id: 1
          enable: True
          uplink_type:
            - type: "upstream"
              intf: "port-channel1"
              state: "present"
            - type: "downstream"
              intf: "ethernet1/1/2-1/1/5"
              state: "present"
          state: "present"
        - id: 2
          enable: True
          state: "present"

 
**Simple playbook to setup uplink - leaf.yaml**

    - hosts: leaf1
      roles:
         - Dell-Networking.dellos-uplink

**Run**

    ansible-playbook -i hosts leaf.yaml

(c) 2019 Dell Inc. or its subsidiaries. All Rights Reserved.
