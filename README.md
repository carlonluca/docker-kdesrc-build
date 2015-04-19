# docker-kdesrc-build

This project aims to provide a lightweight and ready to use KDE development
environment by wrapping the `kdesrc-build` tool in Docker.

This way, you can compile and work on the latest version of the KDE project 
and keep your main system clean of unwanted development packages.

You can run a KDE application on your current desktop by sharing your X
server instance inside the Docker container

Moreover, you can run and try the entire Plasma Desktop in another tty !

# Requirements

- `python` `>=` `3.2`
- `docopt`
- `docker`

# Usage

## Quick and simple

Just choose a distro as a base system for the container (*`archlinux` 
is recommended, as it has the latest dependencies*), and run the script :

    ./run.py --base archlinux

The script will do the following operations :

* check for a `Dockerfile-archlinux` file
* create a dir under `$HOME/kdebuild/archlinux` to be mounted under `/work`
* build or update the Docker image `archlinux-kdedev` if necessary
* mount `bashrc` as a volume (`/home/kdedev/.bashrc`)
* mount `kdesrc-buildrc` as a volume (`/home/kdedev/.kdesrc-buildrc`)
* run the container
* pull the latest version of `kdesrc-build`
* run `kdesrc-build`

You can pass arguments `kdesrc-build` script with the following syntax :

    ./run.py --base archlinux -- --arg1 --arg2 ...

## I want a shell

Just use the `--shell` option. Instead of invoking `kdesrc-build`, it will
run `/bin/bash` so you can work and try whatever you want in the environement.

    ./run.py --base archlinux --shell

## Use a specific version of Qt

If you want to change the Qt version used during the compilation, you can
provide a the path to a Qt installation on the host with :

    ./run.py --base archlinux --qt /path/to/qt

This path will be mounted under `/qt`

Don't forget to change the `qtdir` variable in the `kdesrc-buildrc`

## Run the Plasma Desktop

To run an entire Plasma Desktop session, we need to create a new X server instance
, running on a new `tty`, for example, `tty8`.

As root, on the host system :

    startx -display :1 -- :1 vt8

Also you have to explicitly authorize access to allow clients inside the
container to use this instance :

As root again, on the host system :

    DISPLAY=':1' xhost +

Then, run the container and use the `--display` option to specify the right
tty to be used by applications :

    ./run.py --base archlinux --display ':1' --shell

Execute `startkde` from your install directory (which is `/work` by default)

You should have build `workspace` set (`kdesrc-build workspace`)

Inside the container :

    $ /work/install/bin/startkde

And the KDE desktop should be starting on `tty8` !

## Command line options reference

    Usage: build.py [options] [--shell | -- [<kdesrc-build-args>...]]

    Options:
        -b --base DISTRO        Use DISTRO as base system [Default: all]
        --no-cache              Do not use cache when building the image [Default: False]
        --rm                    Automatically remove the container when it exits [Default: True]
        --root                  Run kdesrc-build as root user. Useful to install files in /usr for example [Default: False]
        --display DISPLAY       Change the DISPLAY environment variable passed to the container [Default: :0]
        --xsocket PATH          Change the PATH to your X server socket dir, which will be mounted as a volume into the container [Default: /tmp/.X11-unix/]
        --qt PATH               Set the PATH to your a specified Qt installation (mounted as /qt) [Default: False]
        -h --help               Display this message

## kdesrc-buildrc configuration

To configure `kdesrc-buildrc`, take a look at [http://kdesrc-build.kde.org/documentation/](http://kdesrc-build.kde.org/documentation/¬)

# Dependencies installed

|              | Archlinux | Fedora | OpenSUSE | Ubuntu |
|--------------|-----------|--------|----------|--------|
| Frameworks   |     ✓     |    ✗   |     ✗    |    ✗   |
| Workspace    |     ✓     |    ✗   |     ✗    |    ✗   |
| Applications |     ✗     |    ✗   |     ✗    |    ✗   |
