## Create new policy module

Problem:
---
type=AVC msg=audit(1637628548.166:957): avc:  denied  { node_bind } for  pid=77662 comm="ping" saddr=127.0.0.1 scontext=system_u:system_r:container_t:s0:c354,c867 tcontext=system_u:object_r:node_t:s0 tclass=icmp_socket permissive=0
---

Solution:
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


## Create new policies for container
1. Run container 

2. Generate inspect info into json file:
~~~
# podman inspect d3dadf1d5ea7 > bash_container.json
~~~

3. Create policy module:
~~~
# udica -j bash_container.json bash_container

Policy bash_container created!

Please load these modules using: 
# semodule -i bash_container.cil /usr/share/udica/templates/{base_container.cil,net_container.cil,home_container.cil}

Restart the container with: "--security-opt label=type:bash_container.process" parameter
~~~

4. Load new policies:
~~~
# semodule -i bash_container.cil /usr/share/udica/templates/{base_container.cil,net_container.cil,home_container.cil}
~~~
