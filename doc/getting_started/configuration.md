# takelage-doc: getting_started/configuration

[Table of contents](../../README.md)

## Getting Started: configuration

takelage uses the GnuPG SSH agent. You need:

- `AddKeysToAgent yes` in your `~/.ssh/config`
- `enable-ssh-support` in your `~/.gnupg/gpg-agent.conf`
- `auto-expand-secmem` in your `~/.gnupg/gpg-agent.conf`
- `gpgconf --launch gpg-agent` in your `.bash_profile`/`.bashrc`
- `export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)`
  in your `.bash_profile`/`.bashrc`

Please make sure that the following lines are **NOT** part
of your configuration:

- `UseKeychain yes` **NOT** in your `~/.ssh/config`

You should use a graphical pinentry program.
On a Mac with [homebrew](https://brew.sh/i) you can install
[pinentry-mac](https://formulae.brew.sh/formula/pinentry-mac):

```bash
brew install pinentry-mac
```

and configure it:

- `pinentry-program /usr/local/bin/pinentry-mac` in your `~/.gnupg/gpg-agent.conf`

You may try to restart your GnuPG agent:

```bash
gpg-connect-agent reloadagent /bye
```

But you may need to reboot your system for the changes to take effect.

Now add your SSH key(s) to the new agent:

```bash
ssh-add ~/.ssh/id_rsa
```

You should init gopass:

```bash
gopass init
```

Then list the key id of you gpg key:

```bash
gpg --list-keys
```

Add the key id of your gpg key as your git signing key
and add your name and e-mail address to your git config:

```bash
git config --global user.signingkey <key-id>
git config --global user.name "my name"
git config --global user.email "myname@example.com"
```

Go into your project directory and run
```bash
tau login
```

Remember that you can always run `tau` with `-l debug`
to see what's going on. Have fun!
