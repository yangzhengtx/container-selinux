https://wiki.centos.org/HowTos/SELinux

## SELinux Tools ##


```diff
List all recent AVCs:
# ausearch -m avc -ts recent

# matchpathcon /tmp/
/tmp	system_u:object_r:tmp_t:s0

# ls -Z /var
     system_u:object_r:acct_data_t:s0 account             system_u:object_r:var_t:s0 gopher               system_u:object_r:var_t:s0 opt
           system_u:object_r:var_t:s0 adm                 system_u:object_r:var_t:s0 kerberos             system_u:object_r:var_t:s0 preserve

Restore SELinux context:
# restorecon -nFRV <pathname>

```

### semanage ###
```diff
Listing the customization fcontext:
# semanage fcontext -l -C

> semanage fcontext -l
SELinux fcontext                                   type               Context

/dev/root                                          block device       system_u:object_r:fixed_disk_device_t:s0 
/etc/named\.root\.hints                            regular file       system_u:object_r:named_conf_t:s0 
/etc/sysconfig/readonly-root                       regular file       system_u:object_r:bin_t:s0 
/home                                              symbolic link      system_u:object_r:home_root_t:s0 
/initrd                                            directory          system_u:object_r:root_t:s0 
/root(/.*)?                                        all files          system_u:object_r:admin_home_t:s0 

Adding fcontext:
# semanage fcontext -a -e <source context> <target context>

Deleting fcontext:
# semanage fcontext -d <target context>

```

### sesearch ##
```diff
Listing the allow rules for specified program:
# sesearch -A -s <domain type>

Listing the allow rules for a given boolean:
# sesearch -A -b <SELinux Boolean>

Listing the rules given source and target context:
# sesearch -A -s <source context> -t <target context>

Listing Transition rules given source and target context:
# sesearch -T -s <source context> -t <target context>

> sesearch -A -t ssh_home_t -c file -p read
allow NetworkManager_ssh_t ssh_home_t:file { append create getattr ioctl link lock open read rename setattr unlink write };
allow NetworkManager_t ssh_home_t:file { append create getattr ioctl link lock open read rename setattr unlink write };
allow NetworkManager_t user_home_type:file { getattr ioctl lock open read };
…… 

> sesearch -A -b container_manage_cgroup
allow container_domain cgroup_t:dir { add_name create getattr ioctl link lock open read remove_name rename reparent rmdir search setattr unlink write }; [ container_manage_cgroup ]:True
allow container_domain cgroup_t:dir { add_name getattr ioctl lock open read remove_name search write }; [ container_manage_cgroup ]:True
allow container_domain cgroup_t:dir { add_name getattr ioctl lock open read remove_name search write }; [ container_manage_cgroup ]:True
allow container_domain cgroup_t:dir { add_name getattr ioctl lock open read remove_name search write }; [ container_manage_cgroup ]:True

> sesearch -A -s sshd_t -t ssh_home_t
allow domain file_type:blk_file map; [ domain_can_mmap_files ]:True
allow domain file_type:chr_file map; [ domain_can_mmap_files ]:True
allow domain file_type:file map; [ domain_can_mmap_files ]:True
allow domain file_type:lnk_file map; [ domain_can_mmap_files ]:True
allow login_pgm ssh_home_t:dir { getattr ioctl lock open read search };
allow login_pgm ssh_home_t:file { getattr ioctl lock open read };
allow login_pgm ssh_home_t:lnk_file { getattr read };

```

### Apply new policy ###
* generate a set of policy rules: audit2allow
* compile: checkmodule
* build: semodule_package
