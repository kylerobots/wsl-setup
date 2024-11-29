# WSL Setup #

This describes common steps to follow when configuring a new WSL Distro for development.

It assumes you start just after installing with `wsl --install <distro-name>`

## Table of Contents ##

1. [Git Setup](#git-setup)

## Git Setup ##

First up is configuring Git to work inside the distro. Git should be installed already, but you will want to configure
it with user information, signing capability, etc.

The file [helpful_files/.gitconfig](helpful_files/.gitconfig) can be used as a reference, with just a few things
modified. Create a new file at _~/.gitconfig_ or copy the template over. Then make the following changes, if desired:

* Set the _user.name_ and _user.email_ fields.
* Add the actual SSH public key under _user.signingkey_. If using 1password, this can be found by opening the item and
copying from there. If storing the SSH key locally, you can us `cat` on the correct _.pub_ file in your _.ssh_ folder on
the computer.
* If not using 1password as an SSH agent, the _gpg "ssh".program_ entry can be removed.
* Configure the rest of the file according to needs. This includes things like setting tag and commit signing or not.

While optional, it is also sometimes helpful when checking commits to create a file called _~/.ssh/allowed_signers_ that
contains a line that looks like this:

```text
<email> ssh-ed25519 <ssh public key>
```

You can then test all this by cloning a GitHub repository or creating a new repository and committing to it.
