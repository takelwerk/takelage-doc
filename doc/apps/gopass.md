# takelage-doc: gopass

[Table of contents](../../README.md)

## gopass Password Manager

[gopass](https://www.gopass.pw/) is a rewrite of 
[pass](https://www.passwordstore.org/) in 
[go](https://golang.org/) with additional features. 
gopass uses 
([for now](https://github.com/gopasspw/gopass/releases/tag/v1.10.0)) 
the same 
[gpg](https://www.gnupg.org/) encrypted text files as pass 
so third-party apps like the ansible 
[passwordstore](https://docs.ansible.com/ansible/latest/plugins/lookup/passwordstore.html)
lookup plugin work. Unlike pass, gopass uses sub-stores
which can be mounted in the password tree with different
[git](https://git-scm.com) origins.

## Versions

gopass is a moving target.
The API changed a lot prior to version 1.10.
This documentation uses 
[gopass 1.10.1](https://github.com/gopasspw/gopass/releases/tag/v1.10.1).

## Definitions

Let `git@example.com:my_project/pass.git`
be the git origin of our password store.
Let `development/my_project` be the mount path of
the password store in the gopass password hierarchy.
Let `/home/myuser/.local/share/gopass/stores/projects-my_project`
be the file system path of your password store.
You can examine the configuration by running `gopass config`.

## Insert a Secret

Use `gopass insert -m` or `gopass insert --multiline` 
to insert new secrets. If you omit `--multiline` the string
'Password: ' will be prepended to the password
which will probably lead to a lot of confusion. 

## Clone a Store

gopass password stores should only be cloned 
on your host system and not in a takelage container.
You have to restart takelage container after you
have cloned a new store so that it becomes available
in the container.
You will be asked for your name and mail address.
These values should correspond to the ones in your git config.

```bash
gopass clone git@example.com:my_project/pass.git projects/my_project
```

## Create a Store

Be sure to add the `--store` argument or you will
reinitialize your root password store! 

```bash
gopass init --store projects/my_project
```

After cloning, gopass tells you the file system path 
of your new store. Change into that directory and 
add a newly created git repository as origin:

```bash
cd /home/myuser/.local/share/gopass/stores/projects-my_project
git remote add origin git@example.com:my_project/pass.git
```

## Delete a Store

Run `gopass config` to get the file system path 
of the store you want to delete.

```bash
gopass mounts umount projects/my_project
rm -fr /home/myuser/.local/share/gopass/stores/projects-my_project
```

## Add or remove recipients

Have a look at who has access to which password store:

```bash
gopass recipients
```

You can add or remove recipients by running
`gopass recipients add` or `gopass recipients remove`.

# Ansible Passwordstore Plugin

By using gopass and the ansible 
[passwordstore](https://docs.ansible.com/ansible/latest/collections/community/general/passwordstore_lookup.html)
plugin we can separate the config
(projects) and code (roles) from the permissions (passwords). 

If each project has its own password store it is possible 
to grant or revoke individual access for each project. 
In case the access list of a password store is changed 
all passwords will be reencrypted. 

If you store the 
[hash](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-generate-encrypted-passwords-for-the-user-module)
of your sudo password in a password store 
accessible by your fellow devops admins 
then they are able to roll out 
new sudo passwords for all admins.

# Integration with takelage-dev

gopass is integrated into
[takelage-dev](https://github.com/geospin-takelage/takelage-dev)
through the
[takelscripts](https://github.com/geospin-takelage/takelage-dev/tree/master/ansible/roles/takel-takelage/files/takelscripts).
The function
[Entrypoint::add_gopass](https://github.com/geospin-takelage/takelage-dev/blob/48d05bd5ca3c300c45739083685306a5a0e5d462/ansible/roles/takel-takelage/files/takelscripts/entrypoint.py#L150)
assumes that the gopass config is located at 
*~/.config/gopass/config.yml* which was not always
the case in older gopass versions.
The config file will be symlinked inside the docker container
The python script then calls `gopass config` and 
parses the output to symlink the password store directories 
inside the docker container.
This way, changes to gopass inside of the container 
will result in changes outside of the container.
 
