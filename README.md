# WordPress Installation on Ubuntu Machines

This project covers the installation of WordPress on virtual machines running the Ubuntu operating system through automation and Ansible commands with Playbook.

## Technologies

- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu)
- [Ubuntu](https://ubuntu.com/download)
- [MySQL](https://www.mysql.com/)
- [Apache](https://httpd.apache.org/)
- [WordPress](https://ubuntu.com/tutorials/install-and-configure-wordpress#1-overview)

## Installation of Dependencies

First, it is necessary to have two virtual machines running: one for the WordPress installation and another for MySQL. For the development of this project, I used VirtualBox:

- [Virtualbox](https://www.virtualbox.org/wiki/Linux_Downloads)

## After Creating the Virtual Machines

Generate SSH keys to access the machines via terminal. For access through VS Code, you can use the "Remote - SSH" extension:

```bash
$ ssh-keygen
$ ssh-copy-id -i key.pub user@ip_address
```

## Next, Install Ansible:
```bash
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible
```

## Add the IP Address of the Virtual Machine to the Hosts File (located at the root of the project):
```bash
[wordpress]
ip_address

[mysql]
ip_address
```

Execute the following command to test the connection (remember to reference the private key):
```bash
ansible wordpress -u user_machine --private-key keys/key -i hosts -m shell -a ‘echo hello, world’
```

### Playbook and dependencies
The main file that triggers the execution of the code for dependency installation and application configuration on the machines is the **provisioning.yaml** file.

The **main.yaml** file within the **"apache/tasks"** folder installs all the necessary dependencies to run the project.

### Installing WordPress

The installation of the WordPress application is configured within the `/wordpress` folder, with the tasks of the playbook listed, along with the handlers and necessary dependencies.

Basically, this folder contains the step-by-step guide for installing WordPress on Ubuntu systems, as outlined in this [link](https://ubuntu.com/tutorials/install-and-configure-wordpress#1-overview) provided earlier.

## Installing MySQL

The installation of the MySQL database is configured within the `/mysql` folder, along with the necessary tasks, including dependency installation, role assignment to the user, file configuration/alteration, creation of user and password, and finally the handlers.

## Variables and Templates

Each machine has a fully dependent installation (WordPress machine and MySQL machine), as well as the configuration of files referencing user and database password variables, IP addresses of the machines.

The files where you need to add this information are as follows:
- `all_example.yaml`
- `mysql_example.yaml`
- `wordpress_example.yaml`

In the **`all_example.yaml`** file, you will find the fields related to the database that need to be filled in (database name, username, and password).
In the **`mysql_example.yaml`** file, only the IP address field of the machine where the database is hosted needs to be filled in.
In the **`wordpress_example.yaml`** file, the fields for the IP address of the machine where WordPress is installed, the installation path of the WordPress directory, and finally, the **salt hash keys** to prevent **known secrets** attacks should be filled in.

## Command to Run the Ansible Playbook

This command executes the Ansible playbook named `provisioning.yaml` using the hosts inventory defined in the `hosts` file. It prompts for the user password with elevated privileges for both machines where WordPress and MySQL are installed:


```bash
$ ansible-playbook provisioning.yaml -i hosts -K
```

PS: The **provisioning.yaml** file references the other files related to WordPress and MySQL, which perform the installation of dependencies, necessary configurations to run the applications, access to the machines via IP address, and creation of user and password.