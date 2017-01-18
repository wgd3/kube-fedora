# kube-fedora

kube-fedora is a project for my home lab that uses Vagrant to spin up a cluster of libvirt-backed VMs, then provision them using Ansible to build a kubernetes cluster. The end result is 1 master node and 2 minion nodes, a flannel overlay network, a single etcd server on the master, and TLS encrypted communication between the services. 

Credit goes to [jameswmills](https://github.com/jameswmills/vagrantfiles) for the SSL-related configuration and ansible plays. 

## Requirements

- CentOS or Fedora bare metal host
- vagrant 
  - vagrant-libvirt plugin
- ansible

## Configuration

There are not many variables to set up with this project. In it's current form, there are no variables defined outside of the `Vagrantfile` for vagrant itself. The 3 VMs that are built have hardcoded hostnames and MAC addresses, the latter of which is used to create a second vNIC in the guest for public access. In my environment I have DNS set up to map these MAC addresses to specific IPs, however the ansible playbook also updates `/etc/hosts` in each VM with FQDN mapping.

On the ansible side there is currently a `group_vars/all.yml` file with a few variables in it. The only one that might be worth updating is the cluster IP range.

## Running kube-fedora

In order to make sure ansible only runs once all nodes are online, it is necessary to use the `--no-parallel` flag with vagrant. Vagrant can be run like this, and will automatically start the ansible playbook:

```
[root@fedorahost kube-fedora]$ vagrant up --no-parallel
```
