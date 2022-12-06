# Overview
This project provides an example of how Netbox data can be converted to network device configuration and applied by Ansible.  It employs Ansible to poll Netbox data and subsequently deploy a VXLAN fabric with BGP EVPN on Cisco NX-OS.  This project is a more advanced version of our previous Arista cEOS project [here](https://github.com/vectornetworks/ansible-netbox-cfg-eos) because it includes a fully BGP EVPN feature set and testing functionality.

## Role/Playbook Information and Requirements

The roles/playbooks in this repo aren't necessarily meant to be run and used; they are just an example of how one might pull data from Netbox and push it using Ansible.  However, they have been run in test environments so if you are looking to give them a spin here's what you need to know.

The Netbox data used in these playbooks is tightly coupled with the data that is built in our [Netbox Vagrant Baselab repository](https://github.com/vectornetworks/netbox-vagrant-baselab).  You could theoretically populate all the required data in Netbox manually, but there are some custom options and tags that you would need to be aware of so it's probably not worth the effort.  If you are interested in all the custom options, what they mean, or how they are used, please feel free to drop us an [email](mailto:info@vectornetworksllc.com) or log an issue.

The main entry point the ```deploy_ls.yml``` playbook, which subsequently calls the 'leaf' and 'spine' roles.  Each role is broken down into invidual config tasks that are tagged as such.  So if, for example, you just wanted to deploy BGP you could execute the ```deploy_ls.yml``` playbook with the ```build_bgp``` tag.

These playbooks were tested against a 1 Spine, 2 Leaf NX-OS topology built on NX-OS Nexus 9000vs in Vagrant.  The playbooks made a couple of quick hacks to get the EVPN BGP peerings up, so more work would be required for extra leafs.  Other than that, this should run fine on both virtual and physical NX-OS devices.

### In summary, here's what you need
* An Ansible control node (from which to run the playbooks)
* An Ansible inventory with hostnames that match the Netbox device names
* A Netbox instance populated with data from the [Vagrant Baselab repository](https://github.com/vectornetworks/netbox-vagrant-baselab), using data from the **ls_data_n9kv.yaml** file.
* A 1 Spine, 2 Leaf NX-OS topology with the NXAPI enabled running over standard HTTP.

## Running the Playbooks
Prior to running the playbooks, you'll need to set your Netbox URL and API key.  This can be accomplished by setting the `NETBOX_URL` and `NETBOX_API_TOKEN` environment variables, respectively. For example:

```
export NETBOX_URL=http://mynetboxhostname/
export NETBOX_API_TOKEN=mynetboxapitoken
```
You could also directly modify the playbooks' variables `nb_api_endpoint` and `nb_api_token` if you prefer to hard set those values.

After the URL and token have been specified and assuming connectivity to the test devices from the Ansible control node, the main playbook can be run with Ansible playbook:

```
ansible-playbook -i <yourinventoryfile> deploy_ls.yml
```

