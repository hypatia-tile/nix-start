# Ad hoc shell environment

In a Nix shell environment,
you can immediately use any program packaged with Nix, without installing it permanently.

You can also share the command invoking such a shell with others,
and it will work on all Linux distributions, WSL, and macOS.

## Create a shell environment

Once you install Nix, you can use it to create new shell environments with programs that you want to use.

In This section you will run two exotic programs called `cowsay` and `lolcat`
taht you will probably not have installed on your machine:

```sh
$ cowsay no can do
zsh: command not found: cowsay
$ echo no change | lolcat
zsh: command not found: lolcat
```

Use `nix-shell` with the `-P`(`--pakcages`) option to specify that we need the `cowsay` and `lolcat` packages:

```sh
$ nix-shell -p cowsay lolcat
these 3 derivations will be built:
...
[nix-shell:~]$
```

## Nested shell sessions

If you need an additional program temporarily, you can run a nexted Nix shell.
The programs provided by the specified packages will be added to the current environment.

```sh
[nix-shell:~]$ nix-shell -p python3
this path will be fetched (11.42 MiB download, 62.64 MiB unpacked):
  /nix/store/...-python3-3.10.11
copying path '/nix/store/....-python-3.11.4' from 'https://cache.nixos.org'...

[nix-shell:~]$ python --version
Python 3.10.11
```

Exit the shell as usual to return to the previous environment.

## Reproducible interpreted scripts

In this tutorial, you will learn how to use Nix to create and run reproducible interpreted scripts,
also knwon as `shebang` scripts.

### Requirements

* A working `Nix installation`
* Familiarity with `Bash`

### A trivial script with non-trivial dependencies

Take the followoing script, which fetches the content XML of a URL, converts it to JSON,
and formats it for better readability:

```bash
#!/bin/bash
curl https://github.com/NixOS/nixpkgs/releases.atom | xml2json | jq .
```

It requires the program `curl`, `xml2json`, and `jq`.
It also requires the `bash` interpreter.
If any of these dependencies are not present on the system running the script,
it will fail partially or altogether.

With Nix, we can declare all dependencies explicitly,
and produce a script that will always run on any machine that supports Nix and the required packages taken from Nixpkgs.

### The script

A `shevang` determines which program to use for running an interpreted script.

We will use the shebang line `$!/usr/bin/env nix-shell`.

`env` is a program available on most modern Unix-like operating systems at the file system path `/usr/bin/env`.
It take a command name as argument and will run the first executable by that name it finds in the directories listed in the environment variable `$PATH`.

...

Make the script executable:

```sh
chomod +x nixpkgs-releases.sh
```

Run the script:

```sh
./nixpkgs-releases.sh
```
