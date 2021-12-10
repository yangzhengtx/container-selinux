## Create new policy 
1. Generate new policies according to audit.log:
~~~
sudo  ausearch -c 'ping' --raw | audit2allow -M my-ping
~~~
Or:
~~~
grep ping /var/log/audit/audit.log | audit2allow -M my-ping
checkmodule -M -m -o my-ping.mod my-ping.te
semodule_package -o my-ping.pp -m my-ping.mod
~~~

2. Check policies in my-ping.te:
~~~
# cat my-ping.te 

module my-ping 1.0;

require {
	type node_t;
	type container_t;
  type spc_t;
	class icmp_socket node_bind;
}

#============= container_t ==============
allow container_t node_t:icmp_socket node_bind;
#============= spc_t ==============
allow spc_t node_t:icmp_socket node_bind;
~~~

3. Load new policies into SELinux:
~~~
# semodule -i my-ping.pp 
# semodule -l |grep ping
my-ping
~~~


## Create new type
