# takelage-doc: getting_started/helloworld

[Table of contents](../../README.md)

## Getting Started: Hello world!

On your host system create and change into a new directory then enter takelage:

```bash
mkdir takelage-helloworld
cd takelage-helloworld
tau login
```

You are warmly greeted with some error messages:

```
[ERROR] Cannot determine project root directory
[INFO] Is there a Rakefile in the project root directory?
takelage: 0.41.0 | tau: 0.32.0 | git: no | gopass: ok | gpg: ok | mutagen: ok | ssh: ok
```

Let's go through it one by one:
tau complains that it cannot determine the root directory of the project. It guesses that a master Rakefile is missing which is correct. We have not yet created the project so that's expected.

Then you see the name of the docker image which is the base for the container you have just started (usually: `takelage`) and the version number. This is very important because the main idea of takelage is to have access to a versioned development and build environment. Of course, a version of tau is available in the container as well.

Then it says `git: no` which is expected as well. Again, we have not yet created the project so of course, a git repository does not exist yet.

At this point you might ask: Why don't we run the "create a minimal project" tau command outside of the container? If we enter the container afterwards these errors were gone. That is true. But if you run the next command outside of the container then _maybe_ it will work on your machine. It definitely works on my machine. If you run the next command in the takelage shell then it works on your machine.

Now create a new takelage packer project for docker images with the name "helloworld":

```bash
tau init packer docker helloworld
```

At the end of the next prompt you should see `(main+)` in orange. This means that you are on the git main branch, all work is committed but you have not yet pushed your work to a remote tracking branch. Uncommitted changes are indicated by a red `(mybranch*)`. When the day‘s work is done always make sure your branch is green (no `*` or `+` next to the branch name for those of us who are red–green color blind).

Collect some system status information:

```bash
tau status
```

Have a look at the project information:

```bash
tau project
```

Here you see your images. There is one image configured with the name "project". The "project" image is the default image which will be used for development. It is based on `takelage/takelslim:latest` which is an official `debian:buster-slim` docker image which has been fully upgraded and which has python3 installed to unleash the power of ansible.

On the same level where the base_user and base_repo are defined you can add a new line where you set a command: echo "Hello world!" This command will be executed when you run a docker container based on the image we are going to build:

```
     omit_pipeline_name: prod
     target_user: takelage
     target_repo: init
+    command: echo "Hello world!"
```

We will build the image with packer using the ansible provisioner.
Now how do we build the image? For sure, there exists one highly complicated command which is probably only slightly different from project to project. In order to remember how to build an image or do some other repetitive task in takelage there is rake. There can be a lot of rake tasks so instead of running rake and getting a list of all available task we run

```bash
take
```

This gives us only a couple of options. As we want to build an image we try the only option with "image" in its name:

```bash
rake images:project:tasks
```

Now the tasks related to images are "unfolded" and we see the command we are looking for:

```bash
rake images:project:prod:build
```

You have built the docker image with packer and packer has comitted it as `packer_local/helloworld-project-prod:latest` to your local docker registry. Have a look:

```bash
docker images
```

Now run it:

```bash
docker run -it packer_local/helloworld-project-prod
```

You should be greeted with: "Hello world!"
