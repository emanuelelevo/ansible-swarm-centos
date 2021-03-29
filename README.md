# Azure docker-swarm scalable cluster with basic TLS

[![Build Status](https://www.travis-ci.com/emanuelelevo/ansible-swarm-centos.svg?branch=main)](https://www.travis-ci.com/emanuelelevo/ansible-swarm-centos)

Set your variables under [group_vars/all.yml](https://github.com/emanuelelevo/ansible-swarm-centos/blob/main/group_vars/all.yml)

The provided certs allow TLS for CN mydockerswarm.com, *.mydockerswarm.com and 127.0.0.1. Current dummy cert passphrase is "secret".

The site.yml outputs the loadbalancer public IP address that may be set on your /etc/hosts file with CN mydockerswarm.com has shown below. 

Local client TLS access over tcp:
```
echo "$LB_OUTPUT_IP mydockerswarm.com" >> /etc/hosts 
cp -r roles/docker_engine/files/pki/* ~/.docker/
export DOCKER_HOST=tcp://mydockerswarm.com:2376 DOCKER_TLS_VERIFY=1
docker node ls
```
Fetch all, manager or worker public ips with the dynamic inventory:
```
ansible all -i ./azure_rm.yml -m debug -a "var=hostvars[inventory_hostname].public_ipv4_addresses" -o
ansible tag_Swarm_manager -i ./azure_rm.yml -m debug -a "var=hostvars[inventory_hostname].public_ipv4_addresses" -o
ansible tag_Swarm_worker -i ./azure_rm.yml -m debug -a "var=hostvars[inventory_hostname].public_ipv4_addresses" -o
```
Scale up managers without running the entire site play with:
```
ansible-playbook scale-up-manager.yml
```
Scale up workers without running the entire site play with:
```
ansible-playbook scale-up-worker.yml
```
Destroy everything under the resource group:
```
ansible-playbook delete_rg.yml
```

