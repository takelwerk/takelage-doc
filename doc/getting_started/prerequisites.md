# takelage-doc: getting_started/prerequisites

[Table of contents](../../README.md)

## Getting Started: prerequisites

You can consider this 
[github actions workflow](https://github.com/takelwerk/takelage-dev/blob/main/.github/workflows/build_test_project_nightly.yml)
as an example on how to install takelage on ubuntu.

On your host system install these prerequisites:
- [docker](https://docs.docker.com/get-docker/)
  If you use linux be sure to follow the
  [postinstall](https://docs.docker.com/engine/install/linux-postinstall/)
  guide
- [gnupg](https://gnupg.org/)
- [git](https://git-scm.com)
- [gopass](https://www.gopass.pw)
- [mutagen](https://mutagen.io/)

For the global takelage `tau` command line interface you'll need `ruby`.
You may use your system ruby but it is recommended to install it
as a non-privileged user:
- [ruby via rvm](https://rvm.io): First install gpg keys then:
```bash
\curl -sSL https://get.rvm.io | bash -s stable --ruby
```
- [tau via gem](https://github.com/takelwerk/takelage-cli) (as user):
```bash
gem install takelage
```
- You can add `tau` autocompletion to your bash startup script:
```bash
source <(tau completion bash)
```
- Use `tau` to get the latest
  [takelage docker image](https://hub.docker.com/r/takelwerk/takelage)
  from hub.docker.com:
```bash
tau update
```
**Warning: *tau update* will call *docker image prune* and remove all dangling images!**
- Every project has its own local command line interface using the
  [rake](https://github.com/ruby/rake) build utility.
  Install rake (as user) like so:
```bash
gem install rake
```
-  The available commands can be inspected by running
```bash
rake
```
