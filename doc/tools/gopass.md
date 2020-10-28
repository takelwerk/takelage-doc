# takelage-doc: gopass

[Table of contents](../../README.md)

## Overview 

- [Introduction](#introduction)
- [Usage](#usage)
- [Ansible](#ansible)
- [Integration](#integration)
- [Miscellaneous](#miscellaneous)

<a name="introduction"/>

# Introduction

## gopass Password Manager

[gopass](https://github.com/gopasspw/gopass)
is a password manager for teams.
gopass has a relatively simple mind set.
passwords are stored in gpg-encrypted text files 
in git repositories which form a password tree.
(go)pass makes use of of a powerful 
[git feature](https://lists.zx2c4.com/pipermail/password-store/2014-May/000940.html):
git can act transparently on gpg files.

[gopass](https://www.gopass.pw/) 
is a rewrite of 
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

You can examine your configuration by running `gopass config`.

<a name="usage"/>

# Usage

## Insert a Secret

Use `gopass insert -m` or `gopass insert --multiline` 
to insert new secrets. If you omit `--multiline` the string
'Password: ' will be prepended to the password
which will probably lead to a lot of confusion. 

## Clone a Store

gopass password stores should only be cloned 
on your host system and not in a takelage container.
Afterwards, you should restart 
all running takelage containers by running `tau clean`
on your host after cloning a new store so
that the changes take effect.
You will be asked for your name and email address.
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
of your new store or you can run `tau config`
to get the path.
Change into that directory and 
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

Have a look at who has access to which password stores:

```bash
gopass recipients
```

You can add or remove recipients by running
`gopass recipients add` or `gopass recipients remove`.

<a name="ansible"/>

# Ansible

## Ansible Passwordstore Plugin

By using gopass and the ansible 
[passwordstore](https://docs.ansible.com/ansible/latest/collections/community/general/passwordstore_lookup.html)
plugin we can separate the config
(projects) and code (roles) from the permissions (passwordstore). 

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

<a name="integration"/>

# Integration

## Integration with takelage-dev

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
 
<a name="miscellaneous"/>
 
# Miscellaneous
 
## Undocumented Feature

The `project.yml` is interpreted as an 
[eRuby](https://en.wikipedia.org/wiki/ERuby) ERB file
so you have the full power of ruby at your disposal.
You may use it for example to 
[call gopass](https://github.com/geospin-takelage/takelage-cli/blob/master/features/cucumber/features/info/info.project.pass.feature)
to inject secrets into every part of takelage.
Just call `tau project` to get a resolved YAML file.
Use it scarcely and wisely!