# Ansible Droplet
[![Travis](https://img.shields.io/travis/FlorianKempenich/ansible-droplet.svg)](https://travis-ci.org/FlorianKempenich/ansible-droplet) [![PyPI](https://img.shields.io/pypi/v/ansible-droplet.svg)](https://pypi.org/project/ansible-droplet/)

`ansible-droplet` is a cli tool to easily create _ready-to-use_ droplets on Digital Ocean.

* Create **ready-to-use** Ubuntu Droplet on Digital Ocean
* One **simple** command: `ansible-droplet create my_droplet`
* Access it directly via its **name**:
  * SSH: `ssh my_droplet`
  * Ansible: `- hosts: my_droplet`
* And more:
  * New sudo user automatically created
  * Swap added (configurable)
  * DigitalOcean advanced metrics enabled
  * `glances` server running to monitor the Droplet from outside
  

## Installation
**Important Note:** For now only `python 2` is supported

```
pip install ansible-droplet
```
> Use a `virtualenv` or install with `pip install ansible-droplet --user`  

--- 

## Usage
### One time setup
Before using the tool, a simple _one-time-setup_ must be done.

1. Make sure you have a **`ssh` public key**, or [generate one](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#generating-a-new-ssh-key)
1. Make sure you have a **Digital Ocean API token**, or [generate one](https://www.digitalocean.com/community/tutorials/how-to-use-the-digitalocean-api-v2)
1. **Store in a file** the Digital Ocean API token in clear<sup>[1](#f1)</sup>
1. Note down:
   * _Path_ to your **ssh public key**
   * _Path_ to the file containing the **Digital Ocean API token**
1. Run `ansible-droplet config` see below for more detail on the config parameters


### Create 
```
ansible-droplet create my_droplet
```
##### Creation process
* **Create a new Droplet** on your Ditigal Ocean Account
* **Set it up** with: Swap, new sudo user, glances server
* **Create a SSH entry** in your `~/.ssh/config` to be able to ssh directly with its name: `ssh my_droplet`
* **Create a ansible inventory entry** in `~/.ansible-droplet-inventory`


### Destroy
```
ansible-droplet destroy my_droplet
```
##### Destruction process
* **Destroy the Droplet** from your Ditigal Ocean Account
* **Remove the SSH entry** from your `~/.ssh/config` to be able to ssh directly with its name: `ssh my_droplet`
* **Remove the ansible inventory entry** from `~/.ansible-droplet-inventory`


## Advanced Usage
### Config parameters
When running `ansible-droplet config` a couple of parameters must be provided:  

* **Path to SSH key**:  
    No brainer, the path to your SSH public key.  
    _Default: `~/.ssh/id_rsa.pub`_

* **Name of SSH key on Digital Ocean**:  
    To prevent uploading the public keys each time, Digital Ocean offers to store them under a name. It can be anything.  
    _Default: `Main SSH Key`_

* **Path to Ditigal Ocean token**:  
    The path to the file containing your Digital Ocean token in plain text.  
    _No defaults_

* **User on Droplet - Username:**  
    Username for the sudo user being created on the Droplet.  
    _No defaults_

* **User on Droplet - Default Password:**
    Default password for the sudo user being created on the Droplet.  
    _No defaults_  
    _Do not forget to change it after the first login!_  


### Droplet specs
You can specify the droplet size and specs when creating a new droplet.  
Simply list it after the droplet name:  
```
ansible-droplet create my_droplet SPEC_NAME
```

For now, only 3 specs are supported<sup>[2](#f2)</sup>:
* **Micro**
  * size: "512mb"
  * region: "lon1" or "fra1"
  * image: "ubuntu-16-04-x64"
  * swap: "4GiB"
* **Mini**
  * size: "1gb"
  * region: "lon1" or "fra1"
  * image: "ubuntu-16-04-x64"
  * swap: "4GiB"
* **Power**
  * size: "4gb"
  * region: "lon1" or "fra1"
  * image: "ubuntu-16-04-x64"
  * swap: "4GiB"

The list of specification are stored on the repository: [Droplet Specs](https://github.com/FlorianKempenich/ansible-droplet/tree/master/ansible_droplet/ansible/droplet_specs)

# Possible sizes are:
[u's-1vcpu-1gb', u'512mb', u's-1vcpu-2gb', u'1gb', u's-3vcpu-1gb', u's-2vcpu-2gb', u's-1vcpu-3gb', u's-2vcpu-4gb', u'2gb', u's-4vcpu-8gb', u'm-1vcpu-8gb', u'c-2', u'4gb', u'c2-2vcpu-4gb', u'g-2vcpu-8gb', u'gd-2vcpu-8gb', u'm-16gb', u's-8vcpu-16gb', u's-6vcpu-16gb', u'c-4', u'8gb', u'c2-4vpcu-8gb', u'm-2vcpu-16gb', u'm3-2vcpu-16gb', u'g-4vcpu-16gb', u'gd-4vcpu-16gb', u'm6-2vcpu-16gb', u'm-32gb', u's-8vcpu-32gb', u'c-8', u'16gb', u'c2-8vpcu-16gb', u'm-4vcpu-32gb', u'm3-4vcpu-32gb', u'g-8vcpu-32gb', u's-12vcpu-48gb', u'gd-8vcpu-32gb', u'm6-4vcpu-32gb', u'm-64gb', u's-16vcpu-64gb', u'c-16', u'32gb', u'c2-16vcpu-32gb', u'm-8vcpu-64gb', u'm3-8vcpu-64gb', u's-20vcpu-96gb', u'48gb', u'm6-8vcpu-64gb', u'm-128gb', u's-24vcpu-128gb', u'64gb', u'c2-32vpcu-64gb', u'm-16vcpu-128gb', u'm3-16vcpu-128gb', u's-32vcpu-192gb', u'm-24vcpu-192gb', u'm-224gb', u'm6-16vcpu-128gb', u'm3-24vcpu-192gb', u'm6-24vcpu-192gb', u'm3-32vcpu-256gb', u'm6-32vcpu-256gb']

# Possible regions are:
[u'ams2', u'ams3', u'blr1', u'fra1', u'lon1', u'nyc1', u'nyc2', u'nyc3', u'sfo1', u'sfo2', u'sfo3', u'sgp1', u'tor1']

### Ansible Droplet Inventory
It is possible to access the Droplet from any other ansible playbook via its **name**:  

    - hosts: my_droplet

For that purpose the file `~/.ansible-droplet-inventory` is created.  
`~/.ansible-droplet-inventory` contains ansible inventory entries for each Droplet created with the `ansible-droplet` tool.

To use the droplet in a playbook, either:
* Point your _inventory_  to the `~/.ansible-droplet-inventory` file
* Point your _inventory_  to a directory containing a symlink to the `~/.ansible-droplet-inventory` file

From there you can reference the Droplet directly by name.


### Multiple configuration - Multiple Digital Ocean accounts

You can use multiple configurations to support:
* Multiple **DigitalOcean accounts**
* Multiple **SHH Keys**
* Multiple **default user/password**

The configuration generated by `ansible-droplet config` is kept in the installation directory.

To allow multiple configs, simply **install multiple versions of `ansible-droplet` in different _virtualenvs_**<sup>[3](#f3)</sup>


---
1. <span id="f1"></span>Yes, this is a security concern. Feel free to open a pull request.
2. <span id="f2"></span>For now, the addition of new droplet specs is not supported. Again, pull requests are welcome :)
3. <span id="f3"></span>This is not optimal... You know what to do ;)
