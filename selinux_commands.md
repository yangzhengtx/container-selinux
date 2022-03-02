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

```
