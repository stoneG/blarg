Title: I moved your stuff but don't freak out cuz I left behind a symlink
Date: 2013-06-29 23:00
Author: Sitong Peng
Tags: *nix-craft, dotfiles, shellscript
Slug: i-moved-your-stuff-but-dont-freak-out-cuz-i-left-behind-a-symlink
Summary: Dotfiles and symlinks otherwise known as cleaning $HOME

<span id="note">Learning new *nix-craft is kinda like being handed shiny new electronics. You're instantly intrigued with what new stuff you can do with it. This entry is the first in a series about *nix-craft.</span>

I'm not sure when I first heard of [symlinking](http://en.wikipedia.org/wiki/Symbolic_link), but the first time it hit me as "something I should get acquainted with" was while I was reading [dotfiles.github.io](http://dotfiles.github.io/). Context clues provided me with the idea that to symlink was to leave a little note to your OS about where you moved the file it expected to find there.

So, as I had endeavored to clean my virtual `$HOME` of dotfiles, it became apparent that I needed to get familiar with symlinks.

Leaving a note
--------------

To create a symlink, you'd type this into your terminal:
```shell
ln -s path/to/target path/to/link
```
In English, this reads as:
Create a symlink at `path/to/link` that redirects to `path/to/target`.

Reading the note
----------------

Most programs play nicely with `symlinks`. For instance, if you were to symlink your `~/.vimrc` to `~/dotfiles/vim/.vimrc`, Vim would access `~/.vimrc` but would end up reading from `~/dotfiles/vim/.vimrc` instead. It's kind of like a HTTP 302 that redirects you to another URI, or in our case, another path on your filesystem. Microsoft Windows users might find symlinks to be similar to shortcuts. They're essentially the same thing with the caveat that Windows programs would unfortunately get cranky if you tried to pass the shortcut file as an executable file. In *nix systems, symlinks in place of the actual file is okay for the most part!

It's also helpful to know what path your existing symlinks are pointing to. A simple `ls` doesn't list out your symlinks. You'll want to run `ls -l`, and symlinks will look something like the second entry below:
```
-rw-------   1 sitong  staff    28054 Jun 29 17:52 .viminfo
lrwxr-xr-x   1 sitong  staff       33 Jun 29 16:57 .vimrc -> /Users/sitong/dotfiles/vim/.vimrc
```
<span id="note">I actually ran `ls -al` since the symlinks in my `$HOME` are all dotfiles.</span>  

Removing the note
-----------------
Removing the symlink is just an `rm` to the symlink. For instance:
```
rm ~/.vimrc
```

A little bonus
--------------
So we saw that `ln -s` is the command for symlinks but what if you passed a naked `ln` command to your terminal?

You would actually be creating a [hard link](http://en.wikipedia.org/wiki/Hard_link). Hard links point directly to a file's memory space instead of a filesystem path as with symlinks. This means you can move or rename the target file and the hard link will still resolve to that original target file. If you were to move or rename your symlinked target file then when some program tries to resolve the symlink, it won't find anything at that path. Try it out yourself!
