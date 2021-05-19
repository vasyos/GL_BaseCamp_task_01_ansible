# GL_BaseCamp_task_01_ansible
![](https://github.com/vasyos/GL_BaseCamp_task_01_ansible/blob/main/instance.JPG)

```
ansible@instance-4:~/ansible$ tree
.
├── ansible.cfg
├── file_creating
│   └── tasks
│       └── main.yml
├── group_vars
│   └── all
├── hosts
├── pb.yml
├── pb_ping.yml
└── version_reading
    └── tasks
        └── main.yml

5 directories, 7 files
```

```
ansible@instance-4:~/ansible$ cat ansible.cfg
[defaults]

inventory = /home/ansible/ansible/hosts
host_key_checking = false
```

```
ansible@instance-4:~/ansible$ cat hosts
[group1]
instance-1 ansible_host=34.118.42.36

[group2]
instance-2 ansible_host=34.118.6.227

[group3]
instance-3 ansible_host=34.118.42.215

[iaas:children]
group1
group2
```
```
ansible@instance-4:~/ansible$ cat group_vars/all
ansible_user: ansible
ansible_ssh_private_key_file: /home/ansible/.ssh/client
```

```
ansible@instance-4:~/ansible$ cat pb.yml
---
- name: create file
  hosts: iaas
  become: yes
  roles:
    - file_creating
- name: read version
  hosts: all
  roles:
    - version_reading

```

```

ansible@instance-4:~/ansible$ cat file_creating/tasks/main.yml
---
  - name: file /etc/iaac with rights 0500
    copy:
      content: ""
      dest: /etc/iaac
      force: no
      group: sys
      owner: root
      mode: 0500
ansible@instance-4:~/ansible$


```

```

ansible@instance-4:~/ansible$ cat version_reading/tasks/main.yml
---
# task file for version_reading
  - name: fetch name/version
    shell: cat /etc/os-release
    register: response

  - name: print name/version
    debug:
      var: response.stdout_lines


```


```
ansible@instance-4:~/ansible$ ansible-playbook pb.yml

PLAY [create file] ***************************************************************************************

TASK [Gathering Facts] ***********************************************************************************
ok: [instance-1]
ok: [instance-2]

TASK [file_creating : file /etc/iaac with rights 0500] ***************************************************
ok: [instance-1]
ok: [instance-2]

PLAY [read version] **************************************************************************************

TASK [Gathering Facts] ***********************************************************************************
ok: [instance-1]
ok: [instance-2]
ok: [instance-3]

TASK [version_reading : fetch name/version] **************************************************************
changed: [instance-1]
changed: [instance-3]
changed: [instance-2]

TASK [version_reading : print name/version] **************************************************************
ok: [instance-3] => {
    "response.stdout_lines": [
        "NAME=\"Ubuntu\"",
        "VERSION=\"20.04.2 LTS (Focal Fossa)\"",
        "ID=ubuntu",
        "ID_LIKE=debian",
        "PRETTY_NAME=\"Ubuntu 20.04.2 LTS\"",
        "VERSION_ID=\"20.04\"",
        "HOME_URL=\"https://www.ubuntu.com/\"",
        "SUPPORT_URL=\"https://help.ubuntu.com/\"",
        "BUG_REPORT_URL=\"https://bugs.launchpad.net/ubuntu/\"",
        "PRIVACY_POLICY_URL=\"https://www.ubuntu.com/legal/terms-and-policies/privacy-policy\"",
        "VERSION_CODENAME=focal",
        "UBUNTU_CODENAME=focal"
    ]
}
ok: [instance-1] => {
    "response.stdout_lines": [
        "PRETTY_NAME=\"Debian GNU/Linux 10 (buster)\"",
        "NAME=\"Debian GNU/Linux\"",
        "VERSION_ID=\"10\"",
        "VERSION=\"10 (buster)\"",
        "VERSION_CODENAME=buster",
        "ID=debian",
        "HOME_URL=\"https://www.debian.org/\"",
        "SUPPORT_URL=\"https://www.debian.org/support\"",
        "BUG_REPORT_URL=\"https://bugs.debian.org/\""
    ]
}
ok: [instance-2] => {
    "response.stdout_lines": [
        "NAME=\"Ubuntu\"",
        "VERSION=\"16.04.7 LTS (Xenial Xerus)\"",
        "ID=ubuntu",
        "ID_LIKE=debian",
        "PRETTY_NAME=\"Ubuntu 16.04.7 LTS\"",
        "VERSION_ID=\"16.04\"",
        "HOME_URL=\"http://www.ubuntu.com/\"",
        "SUPPORT_URL=\"http://help.ubuntu.com/\"",
        "BUG_REPORT_URL=\"http://bugs.launchpad.net/ubuntu/\"",
        "VERSION_CODENAME=xenial",
        "UBUNTU_CODENAME=xenial"
    ]
}

PLAY RECAP ***********************************************************************************************
instance-1                 : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    i                                                                                   gnored=0
instance-2                 : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    i                                                                                   gnored=0
instance-3                 : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    i                                                                                   gnored=0

```

![](https://github.com/vasyos/GL_BaseCamp_task_01_ansible/blob/main/playbook.JPG)
