
ansible-role-slack
=======================

An Ansible role to install and manage **slack**


Description
-----------

Very Rough Draft


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
# file: roles/slack/defaults/main.yml
#

# probably set in playbook and/or group_vars
slack_controller_home:              : '{{ fact_controler_home }}'

# probably set in this or a dependent role

slack_state                   : 'present'
slack_installation_type       : 'local' # 'local', 'url' # is url local or remote.
slack_app_name                : 'slack-desktop'
slack_ver                     : '2.6.2'
slack_arch                    : 'amd64'
slack_package_type            : 'deb'
slack_package_filename	      : '{{ slack_app_name }}-\
                                 {{ slack_ver }}-{{ slack_arch }}.\
                                 {{ slack_package_type }}' # example builds "slack-desktop-2.6.2-amd64.deb"
slack_package_url             : 'file://~src/Ubuntu/16.04/slack/{{ slack_ver }}/{{ slack_package_filename }}'
slack_local_resource_location : 'src/Ubuntu/16.04/slack/{{ slack_ver }}/{{ slack_package_filename }}
```


Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles. Examples:

* [ ansible-role-acemenu ]( https://github.com/cjsteel/ansible-role-acemenu )
* [ ansible-role-ensure_dirs ]( https://github.com/csteel/ansible-role-ensure_dirs )
* [ ansible-role-skel ]( https://github.com/csteel/ansible-role-skel )

### ensure_dirs

#### roles/ansible-role-slack/meta/main.yml

```yaml
---
# file: roles/ansible-role-slack/meta/main.yml in dependant role
dependencies:

- { role: ensure_dirs, 
        ensure_dirs_on_remote: "{{ slack_remote_directories_description }}",
        ensure_dirs_on_local : "{{ slack_local_directories_description }}"
  }
```

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


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
    - hosts: servers
      roles:
         - { role: cjsteel.ansible-role-slack, x: 42 }
```



## License

Various, MIT



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