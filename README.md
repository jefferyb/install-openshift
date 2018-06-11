# Install RedHat OpenShift Origin

## Installation

1- Edit `extra-vars/answers.yaml` file with your own settings or create your own, like:

```bash
$ cp extra-vars/answers.yaml extra-vars/example.com-vars.yaml
# edit it with your own variables
vim extra-vars/example.com-vars.yaml
```

2- Clone https://github.com/openshift/openshift-ansible.git

```bash
$ ansible localhost --extra-vars "@extra-vars/answers.yaml" -m git -a 'repo=https://github.com/openshift/openshift-ansible.git dest=openshift-ansible version="{{ RELEASE_VERSION }}"'
```

3- Create your inventory file

```bash
$ ansible localhost --extra-vars "@extra-vars/answers.yaml" -m template -a "src=files/inventory.tmpl dest=inventory.install"
```

4- Check the inventory file, inventory.install, and then start the install

```bash
# ping to check connection
$ ansible -i inventory.install all -m ping

# start the install
$ ansible-playbook --extra-vars "@extra-vars/answers.yaml" -i inventory.install install-openshift.yaml
```

5- After the install, you'll get a message with the link to the console to login into

## (Optional) Setup the NFS Provisioner 

(https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client)

```bash
### Edit extra-vars/answers.yaml or create your own and make sure "nfs_ip_addresses" and "masters_ip_addresses.external" are set:
#
### Create a hosts file
$ ansible localhost --extra-vars "@extra-vars/answers.yaml" -m template -a "src=files/inventory.tmpl dest=inventory.install"
#
### Start the install
$ ansible-playbook --extra-vars "@extra-vars/answers.yaml" -i inventory.install playbooks/setup-nfs-provisioner.yaml
```

## Add new node hosts

https://docs.openshift.org/latest/install_config/adding_hosts_to_existing_cluster.html#adding-nodes-advanced

Edit/add/comment out the "new_nodes_ip_addresses" section with the IPs of the new node(s) in extra-vars/answers.yaml

```bash
# Clone https://github.com/openshift/openshift-ansible.git, if you haven't
$ ansible localhost --extra-vars "@extra-vars/answers.yaml" -m git -a 'repo=https://github.com/openshift/openshift-ansible.git dest=openshift-ansible version="{{ RELEASE_VERSION }}"'

# Create/update your inventory file
      # edit "new_nodes_ip_addresses" in extra-vars/answers.yaml
      new_nodes_ip_addresses:
        - external: 10.10.10.80
          internal: 192.168.0.80
          node_labels: "{'region': 'buildnode', 'zone': 'default'}"

$ ansible localhost --extra-vars "@extra-vars/answers.yaml" -m template -a "src=files/inventory.tmpl dest=inventory.install" --diff

# Check the inventory file, inventory.install, ping the nodes, and then start the install
$ ansible -i inventory.install all -m ping
$ ansible-playbook --extra-vars "@extra-vars/answers.yaml" -i inventory.install add-hosts-to-existing-cluster.yaml
```

## Uninstalling OpenShift Origin

You can uninstall OpenShift Origin hosts in your cluster by running the uninstall.yml playbook. This playbook deletes OpenShift Origin content installed by Ansible, including:

Configuration

Containers

Default templates and image streams

Images

RPM packages

The playbook will delete content for any hosts defined in the inventory file that you specify when running the playbook. If you want to uninstall OpenShift Origin across all hosts in your cluster, run the playbook using the inventory file you used when installing OpenShift Origin initially or ran most recently:

```bash
$ ansible-playbook -i inventory.install openshift-ansible/playbooks/adhoc/uninstall.yml
```

### Resources:

+ https://docs.openshift.org/latest
+ https://github.com/openshift/openshift-ansible
+ https://github.com/gshipley/installcentos
+ https://medium.com/@james_devcomb/adding-wildcard-certificate-to-default-openshift-origin-router-77402464a782

