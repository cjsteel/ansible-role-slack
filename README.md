
ansible-role-slack
=======================

An Ansible role to install and manage **slack**


Description
-----------

Very Rough Draft

## Notes

You may want to remove / uninstall older versions before installing newer ones.


Resources
---------

-  https://slack.com/



Requirements
------------




Options
-------


Issues
------


Role Variables
--------------

### roles/slack/defaults/main.yml

```yaml
---
# file: roles/slack/defaults/main.yml
#

# Requirements
#
# set the value of project_deployment_user_name in your projects group_vars/all/defaults.yml
#
# ---
# file: group_vars/all/defaults.yml
#
# project_deployment_user_name: 'deployment_user_name'

slack_controller_home   : '{{ fact_controler_home }}'
slack_remote_user       : '{{ project_deployment_user_name }}'
slack_remote_users_home : '/home/users/{{ slack_remote_user }}'

# probably set in this or a dependent role

slack_state             : 'absent' # 'present' # 'absent'
#slack_installation_type : 'local' # 'url'
slack_app_name          : 'slack'
slack_package_name      : 'slack-desktop'
slack_ver               : '2.0.3' #'2.6.2' # 2.0.3
slack_arch              : 'amd64'
slack_package_type      : 'deb'

# calculated vars

# example below builds "slack-desktop-2.6.2-amd64.deb"
slack_package_filename  : '{{ slack_package_name }}-{{ slack_ver }}-{{ slack_arch }}.{{ slack_package_type }}'
slack_controller_package_path : '{{ fact_controller_home }}/src/Ubuntu/16.04/slack/{{ slack_ver }}/{{ slack_package_filename }}'

slack_taget_node_package_dir  : '{{ slack_remote_users_home }}/src/Ubuntu/16.04/slack/{{ slack_ver }}'
slack_taget_node_package_path : '{{ slack_taget_node_package_dir }}/{{ slack_package_filename }}'
```

### roles/slack/tests/vagrant.yml

```shell
---
# file: roles/{{ short_role_name }}/tests/vagrant.yml

- hosts: all
  remote_user: ubuntu
  become: false # or local directory creation will fail
  pre_tasks:

    - set_fact: fact_controller_user="{{ lookup('env','USER') }}"
    - debug: var=fact_controller_user

    - set_fact: fact_controller_home="{{ lookup('env','HOME') }}"
    - debug: var=fact_controller_home

  vars:

    - slack_controller_home   : '{{ fact_controler_home }}'
    - slack_remote_user       : 'ubuntu'
    - slack_remote_users_home : '/home/ubuntu'

# probably set in this or a dependent role

    - slack_state                   : 'absent' # 'present' # 'absent'
#    - slack_installation_type       : 'local' # 'url'
    - slack_app_name                : 'slack'
    - slack_package_name            : 'slack-desktop'
    - slack_ver                     : '2.0.3' #'2.6.2' # 2.0.3
    - slack_arch                    : 'amd64'
    - slack_package_type            : 'deb'
    # example below builds "slack-desktop-2.6.2-amd64.deb"
    - slack_package_filename        : '{{ slack_package_name }}-{{ slack_ver }}-{{ slack_arch }}.{{ slack_package_type }}'

    - slack_controller_package_path : '{{ fact_controller_home }}/src/Ubuntu/16.04/slack/{{ slack_ver }}/{{ slack_package_filename }}'

    - slack_taget_node_package_dir  : '{{ slack_remote_users_home }}/src/Ubuntu/16.04/slack/{{ slack_ver }}'
    - slack_taget_node_package_path : '{{ slack_taget_node_package_dir }}/{{ slack_package_filename }}'

  roles:
    - ../../
```



NOT IN USE AT THIS TIME - Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles. Examples:

* [ ansible-role-acemenu ]( https://github.com/cjsteel/ansible-role-acemenu )
* [ ansible-role-ensure_dirs ]( https://github.com/csteel/ansible-role-ensure_dirs )
* [ ansible-role-skel ]( https://github.com/csteel/ansible-role-skel )

### NOT IN USE AT THIS TIME  - ensure_dirs

#### NOT IN USE AT THIS TIME - roles/ansible-role-slack/meta/main.yml

```yaml
---
# file: roles/ansible-role-slack/meta/main.yml in dependant role
dependencies:

- { role: ensure_dirs, 
        ensure_dirs_on_remote: "{{ slack_remote_directories_description }}",
        ensure_dirs_on_local : "{{ slack_local_directories_description }}"
  }
```

#### NOT IN USE AT THIS TIME 

#### roles/ansible-role-slack/defaults/main.yml example

```yaml
slack_remote_directories_description:

  slack_installation_resources_dir:

    state       : "present"					# absent
    path        : "sys/sw"					# relative to Ansible users home
    owner       : "{{ ansible_ssh_user }}"
    group       : "{{ ansible_ssh_user }}"
    mode        : "0644"

slack_local_directories_description:

  slack_installation_resources_dir:

    state       : "present"					# absent
    path        : "sys/sw/" 				# relative to Ansible users home dir
    owner       : "{{ ansible_ssh_user }}"
    group       : "{{ ansible_ssh_user }}"
    mode        : "0644"
```



role playbook example
----------------

### project_name/slack.yml

```yaml
---
# file: project_name/slack.yml

- hosts: slack
  become: false
  gather_facts: true
  pre_tasks:

    - set_fact: fact_controller_user="{{ lookup('env','USER') }}"
    - debug: var=fact_controller_user

    - set_fact: fact_controller_home="{{ lookup('env','HOME') }}"
    - debug: var=fact_controller_home

  roles:

    - { role: slack, slack_state: 'absent', slack_ver: '2.0.3' }
    - { role: slack, slack_state: 'present', slack_ver: '2.6.2' }

#    - { role: cjsteel.ansible-role-slack, slack_state: 'absent', slack_ver: '2.0.3' }
#    - { role: cjsteel.ansible-role-slack, slack_state: 'present', slack_ver: '2.6.2' }
```



## main playbook example

### project_name/systems.yml

```yaml
---
- hosts: all
  become: false

- include: deployment_user.yml

- include: shorewall.yml

- include: ldap.yml

- include: workstation.yml

- include: slack.yml
...
```



## Ansible command examples

### without sudo

```shell
ansible-playbook -i inventory/dev systems.yml --limit ace-ws-77
```

### with sudo

```shell
ansible-playbook -i inventory/dev systems.yml --limit ace-ws-77 --ask-become-pass
```



## Role Testing

### Locally using vagrant

```shell
mkdir .vagrant/synced
vagrant up
vagrant ssh -- -X
# slack-desktop # ver 2.0.3 ?
slack # ver 2.6.2
exit
vagrant destroy
```

### Error via X tunnel

"**slack: error while loading shared libraries: libasound.so.2: cannot open shared object file: No such file or directory**"

- libasound.so.2 is a part of "alsa-lib".
- Package "alsa-utils" depends on "alsa-lib", and installs with them, perhaps alsa-lib should be added to dependencies list?

Resolved with:

```shell
sudo apt install alsa-utils
```

Give new error but starts slack

```shell
Gtk-Message: Failed to load module "canberra-gtk-module"
Creating Slack Application
```

#### error reference

* [https://aur-dev.archlinux.org/packages/slack-desktop/?](https://aur-dev.archlinux.org/packages/slack-desktop/?)

## License

MIT

## Author Information

Christopher Steel on behalf of ACElabs  
Systems Administrator  
McGill Centre for Integrative Neuroscience  
Montreal Neurological Institute  
E-mail: christopherDOTsteel@mcgill.ca  
[mcin-cnim.ca](http://mcin-cnim.ca/)    
[theneuro.ca](http://www.mcgill.ca/neuro/)   

## Open Science

The Montreal Neurological Institute has adopted the principles of Open Science. We are inspired by the likes of the Allen Institute for Brain Science, the National Institutes of Health's Human Connectome project, and the Human Genome project. For additional information please see [open science at the neuro]( https://www.mcgill.ca/neuro/open-science-0).



![MCIN](imgs/mcin-logo-brain-140x116.png)          ![neuro](imgs/neuro-logo-160x116.png)  