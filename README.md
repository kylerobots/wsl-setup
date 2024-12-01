# WSL Setup #

This describes common steps to follow when configuring a new WSL Distro for development.

It assumes you start just after installing with `wsl --install <distro-name>`

## Table of Contents ##

1. [Password-Less Sudo](#password-less-sudo)
2. [Preserve Proxy Settings](#preserve-proxy-settings)
3. [Git Setup](#git-setup)
4. [Python](#python)
    1. [pyenv](#pyenv)
    2. [pipx](#pipx)
    3. [Poetry](#poetry)
5. [C++](#c)
    1. [CMake](#cmake)
    2. [Other Build Tools](#other-build-tools)
    3. [vcpkg](#vcpkg)

## Password-Less Sudo ##

This is probably not recommended, but it is nice to not need to enter your password every time you run a `sudo` command.
To do that, run `sudo visudo` and add this line to the bottom of the file that opens. Be sure to replace _<$USER>_ with
your actual user name, not the environment variable name.

```text
<$USER> ALL=(ALL) NOPASSWD:ALL
```

Then, just restart the distro and/or close and reopen all terminals.

## Preserve Proxy Settings ##

If working in an environment with a proxy, you can also have sudo always use the proxy settings by default. This will
help avoid the need to add `-E` to every `sudo` command. To make this change, run `sudo visudo` again, find the line
listed below, and uncomment it. Note that it might look slightly different on older distributions.

```text
Defaults:%sudo env_keep += "http_proxy https_proxy ftp_proxy all_proxy no_proxy"
```

Then, just restart the distro and/or close and reopen all terminals.

## Git Setup ##

First up is configuring Git to work inside the distro. Git should be installed already, but you will want to configure
it with user information, signing capability, etc.

The file [helpful_files/.gitconfig](helpful_files/.gitconfig) can be used as a reference, with just a few things
modified. Create a new file at _~/.gitconfig_ or copy the template over. Then make the following changes, if desired:

* Set the _user.name_ and _user.email_ fields.
* Add the actual SSH public key under _user.signingkey_. If using 1password, this can be found by opening the item and
copying from there. If storing the SSH key locally, you can us `cat` on the correct _.pub_ file in your _.ssh_ folder on
the computer.
* If not using [1password](https://developer.1password.com/docs/ssh/git-commit-signing/) as an SSH agent, the
_gpg "ssh".program_ entry can be removed.
* Configure the rest of the file according to needs. This includes things like setting tag and commit signing or not.

Especially if using 1password, it is useful to alias the ssh commands to their _.exe_ versions. Open or create the
_~/.bash_aliases_ file and add the following lines. Then either restart the terminal or source _~/.bashrc_.

```bash
alias ssh='ssh.exe'
alias ssh-add='ssh-add.exe'
```

While optional, it is also sometimes helpful when checking commits to create a file called _~/.ssh/allowed_signers_ that
contains a line that looks like this:

```text
<email> ssh-ed25519 <ssh public key>
```

You can then test all this by cloning a GitHub repository or creating a new repository and committing to it.

## Python ##

Python development will largely rely on a few parts to make the setup a bit more modular.

### [pyenv](https://github.com/pyenv/pyenv) ###

Pyenv will be used for Python version management. The install steps are very well documented at [https://github.com/pyenv/pyenv?tab=readme-ov-file#installation](https://github.com/pyenv/pyenv?tab=readme-ov-file#installation).
Be sure to follow not just the install steps, but also the shell and build steps.

Once installed, install at least one version of Python. Just run `pyenv install <version>`. If installing version 3.13
or higher, you can look for a version of _3.13.0t_ or similar to disable GIL.

To set one of these versions as the default system wide, just run `pyenv global <version>`.

### [pipx](https://pipx.pypa.io/latest/) ###

Pipx is helpful for installing Python applications totally independent from any other Python environment, to ensure no
dependency conflicts.

The install instructions are available at
[https://pipx.pypa.io/latest/installation/#on-linux](https://pipx.pypa.io/latest/installation/#on-linux). On newer
Ubuntu distributions, the instructions are just as follows.

```bash
sudo apt update
sudo apt install pipx
pipx ensurepath
```

Additionally, enabling tab completion can be helpful: `pipx completions`.

### [Poetry](https://python-poetry.org/) ###

Poetry is a dependency management tool to help ensure your development projects don't conflict with each other. It also
helps with building and deploying packages, among other things.

There are multiple ways to install it, as described at
[https://python-poetry.org/docs/](https://python-poetry.org/docs/). Since pipx is already installed, that is probably
the easiest. To specify a particular Python version to use with pipx, run
`pipx install poetry --python $(which python)`, where the last argument is the path to the particular version.

## C++ ##

C++ development will be a bit more direct. For more modular, use Docker.

### [CMake](https://cmake.org/) ###

CMake will help generate and run the compilation instructions. While it is possible to install with `apt-get`, this will
typically be an older version. If you want the latest (or a specific version), use the script
[helpful_files/reinstall-cmake.sh](helpful_files/reinstall-cmake.sh) like shown below, where _version-number_ is
replaced with the desired version (e.g. 3.31.1).

```bash
./helpful_files/reinstall-cmake.sh version-number
```

This file is borrowed from Microsoft's C++ container setup template.

### Other Build Tools ###

There are various compilers and utilities that may be helpful to download. A lot of them are available through `apt`, so
can be downloaded with `apt install`. Some may have options to download specific versions. A short list of packages is
as follows. There may be some overlap between packages and dependencies.

* build-essential (gcc, make)
* clang
* clang-format
* clang-tidy
* cppcheck
* gdb
* lldb
* llvm
* ninja-build
* valgrind

Here is a convenient way to install them all.

```bash
sudo apt-get update
sudo apt-get install build-essential clang clang-format clang-tidy cppcheck gdb lldb llvm ninja-build valgrind
```

### [vcpkg](https://vcpkg.io/en/) ###

vcpkg can help with C++ package management. It's install instructions are straightforward, as described at
[https://learn.microsoft.com/en-us/vcpkg/get_started/get-started?pivots=shell-bash](https://learn.microsoft.com/en-us/vcpkg/get_started/get-started?pivots=shell-bash)
in steps 1 and 2. It can go anywhere. I choose to put it in a folder called _~/.vcpkg_. As long as the environmental
variable paths are correct, it will work.

Note that any project using CMake should also have a _CMakeUserPresets.json_ file that points to the correct location
for vcpkg, as shown in the example linked above in step 4.
