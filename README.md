# `dotman`: a simple dotfiles manager based on `Python3`

`dotman` is a python package that serves as a simple manager for the
dotfiles. The code structure is inspired by
[`dotfiles`][https://github.com/jbernard/dotfiles].

`dotman` allows storing your dotfiles in a single folder by
maintaining the original file structure. The folder can be distribute
by VCS system or file syncronization services and deployed onto
different systems using standard VCS branching.

By default, `dotman` stores the files in the synchronized folder by
symbol links. You can also specify if the files should be copied
instead of symlink.

## Installation
`dotman` requires `Python` version larger than 3.4. Currently the
installation is done using git, PyPI will come later.

Most likely, the `dotman` package should be installed outside a
particular python virtual environment, run the following codes to
install the `HEAD` version:

    $ git clone https://github.com/jbernard/dotfiles
    $ cd dotfiles
    $ git submodule update --init
    $ ./bin/dotfiles --help

Note: the source tree example above will run whatever code has been
committed to your current checkout, whereas pip will fetch the latest
official version from pypi.  This might be what you want, but you should
be aware.

## Getting Help And Discovering Commands

`dotfiles` uses click for its CLI interface, so every subcommand accepts
the `--help` flag to offer additional information on what is available.
The aim is for this information to be sufficient for use.  At some point
I'll write a manpage, but do file a bug if any of the usage information
is inaccurate or misleading.

## Interface

`-a, --add <file...>`
    Add dotfile(s) to the repository.

`-c, --check`
    Check for missing or unsynced dotfiles.

`-l, --list`
    List currently managed dotfiles, one per line.

`-r, --remove <file...>`
    Remove dotfile(s) from the repository.

`-s, --sync [file...]`
    Update dotfile symlinks. You can overwrite colliding files with `-f` or
    `--force`.  All dotfiles are assumed if you do not specify any files to
    this command.

`-m, --move <path>`
    Move dotfiles repository to another location, updating all symlinks in the
    process.

For all commands you can use the `--dry-run` option, which will print actions
and won't modify anything on your drive.

## Installation

To install dotfiles, simply:

    $ pip install dotfiles

Or, if you absolutely must:

    $ easy_install dotfiles

But, you really shouldn't do that.

If you want to work with the latest version, you can install it from `the
repository`_:

    $ git clone https://github.com/jbernard/dotfiles
    $ cd dotfiles
    $ ./bin/dotfiles --help

## Examples

To install your dotfiles on a new machine, you might do this:

    $ git clone https://github.com/me/my-dotfiles Dotfiles
    $ dotfiles --sync

To add '~/.vimrc' to your repository:

    $ dotfiles --add ~/.vimrc     (relative paths work also)

To make it available to all your hosts:

    $ cd ~/Dotfiles
    $ git add vimrc
    $ git commit -m "Added vimrc, welcome aboard!"
    $ git push

You get the idea. Type `dotfiles --help` to see the available options.

## Configuration

You can choose to create a configuration file to store personal customizations.
By default, `dotfiles` will look for `~/.dotfilesrc`. You can change this
with the `-C` flag. An example configuration file might look like:

    [dotfiles]
    repository = ~/Dotfiles
    ignore = [
        '.git',
        '.gitignore',
        '*.swp']
    externals = {
        '.bzr.log':     '/dev/null',
        '.uml':         '/tmp'}

You can also store your configuration file inside your repository. Put your
settings in `.dotfilesrc` at the root of your repository and `dotfiles` will
find it. Note that `ignore` and `externals` are appended to any values
previously discovered.

Prefixes
--------

Dotfiles are stored in the repository with no prefix by default. So,
`~/.bashrc` will link to `~/Dotfiles/bashrc`. If your files already have a
prefix, `.` is common, but I've also seen `_`, then you can specify this
in the configuration file and `dotfiles` will do the right thing. An example
configuration in `~/.dotfilesrc` might look like:

    [dotfiles]
    prefix = .

Externals
---------

You may want to link some dotfiles to external locations. For example, `bzr`
writes debug information to `~/.bzr.log` and there is no easy way to disable
it. For that, I link `~/.bzr.log` to `/dev/null`. Since `/dev/null` is
not within the repository, this is called an external. You can have as many of
these as you like. The list of externals is specified in the configuration
file:

    [dotfiles]
    externals = {
        '.bzr.log':     '/dev/null',
        '.adobe':       '/tmp',
        '.macromedia':  '/tmp'}

Ignores
-------

If you're using a VCS to manage your repository of dotfiles, you'll want to
tell `dotfiles` to ignore VCS-related files. For example, I use `git`, so
I have the following in my `~/.dotfilesrc`:

    [dotfiles]
    ignore = [
         '.git',
         '.gitignore',
         '*.swp']

Any file you list in `ignore` will be skipped. The `ignore` option supports
glob file patterns.

Packages
--------

Many programs store their configuration in `~/.config`. It's quite cluttered
and you probably don't want to keep all its content in your repository. For this
situation you can use the `packages` setting:

    [dotfiles]
    packages = ['config']

This tells `dotfiles` that the contents of the `config` subdirectory of
your repository must be symlinked to `~/.config`. If for example you have a
directory `config/awesome` in your repository, it will be symlinked to
`~/.config/awesome`.

This feature allows one additional level of nesting, but further subdirectories
are not eligible for being a package.  For example, `config` is valid, but
`config/transmission` is not valid.  Arbitrary nesting is a feature under
current consideration.

At the moment, packages can not be added or removed through the command line
interface.  They must be constructed and configured manually.  Once this is
done, `sync`, `list`, `check`, and `move` will do the right thing.
Support for `add` and `remove` is a current TODO item.

Contribute
----------

If you'd like to contribute, simply fork [the
repository](https://github.com/jbernard/dotfiles), commit your changes, make
sure tests pass, and send a pull request. Go ahead and add yourself to
[AUTHORS](AUTHORS.md) or I'll do it when I merge your changes.
