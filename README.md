# VM Stack Demo feat. php7 and Symfony3

This is a skeleton setup for a development VM running the following stack:

- Nginx 1.10
- PHP 7.1 (fpm)
- MariaDB 10.2
- Postfix MTA

And also has Symfony 3.x pre-installed with some most common bundles ready to kick-start your work

#### Prerequisites

* VirtualBox 5.1+
* Vagrant 1.8+ (with vagrant-hostupdater plugin; vagrant-cachier recommended)

#### Step-by-step

1) Clone project repository (this one):
```
git clone git@github.com:mkilmanas/vm-stack-php7-symfony3.git
```

2) Create Ansible vault password file at `tools/ansible/.vault-secret-key` and put the random string in it. This will be the encryption key (never commit this file! there is a `.gitignore` entry for that)

3) Rekey the vault file:
```
ansible-vault rekey tools/ansible/vars/dev-vault.yml
```
Old password is `change-me-now`, and the new one is whatever you put in the `.vault-secret-key` file

4) Run vagrant to create and provision your VM
```
cd tools/vagrant/
vagrant up
```
Don't worry if it takes very long the first time. There are a couple of demanding tasks to be executed (namely upgrading OS packages to the latest versions and installing vendor dependencies with composer). It should normally complete everything with 10-20 minutes. Subsequent provisioning is *WAY* quicker.

5) You now have a working webserver which you can access at [http://vm-stack-demo.dev/](http://vm-stack-demo.dev/)

#### Other tips and advices

* Most of the configuration values (and basic credentials) are stored in [tools/ansible/vars/dev.yml](tools/ansible/vars/dev.yml)
* Sensitive credentials *should* be stored encrypted in [tools/ansible/vars/dev-vault.yml](tools/ansible/vars/dev-vault.yml). See [Ansible Docs](http://docs.ansible.com/ansible/playbooks_vault.html) how to work with these vault files. Remember the password is stored in the secret file which is also used by vagrant
* If you ever need to add new parameters to `app/config/parameters.yml` file, you need to add it to [tools/ansible/roles/webserver/templates/parameters.yml.dist.j2](tools/ansible/roles/webserver/templates/parameters.yml.dist.j2) file. You also have to provide default values there, which will be copied to `parameters.yml` by composer. Do NOT add values directly to template if they are likely to be different among evironments - use var files for that.
* You can run `cd tools/vagrant/ && vagrant provision` at any time to propagate latest environment changes. All new changes MUST be done in a way that it does not fail on subsequent/repeated runs of the provisioner (i.e. validate your pre-requisites, don't assume; also try to avoid reruning the task if it has no new changes)
    