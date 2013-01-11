Title: I am a real Vim user today
Date: 2013-01-10 20:00
Author: Sitong Peng
Tags: vim, vimrc, shortcut, emacs, word wrap, blog, hacker school
Slug: i-am-a-real-vim-user-today
Summary: My first Vim shortcut

There comes a point, usually after you've been using something for awhile, when you realize you are no longer just using it casually. ***And no, I'm not talking about drugs.***I'm talking about defining my first shortcut key in my `.vimrc`.

A little background
-------------------
I was first introduced to [Vim](http://www.vim.org/) by my [Hacker School](http://www.hackerschool.com) buddy [James](http://www.jamesshieh.com). For those of you who don't know, Vim is a very peculiar text editor. It is an "improved" version of [Vi](http://ex-vi.sourceforge.net/), the de facto Unix editor. Vim allows for a very efficient coding experience in which your hands generally never have to leave their main resting positions on the keyboard. And unlike editors (see [Emacs](http://www.gnu.org/software/emacs/)), Vim makes heavy usage of different input modes to separate document navigation, inserting content, and text selection. This makes for a steep initial learning curve, as the default mode in Vim is mostly navigational, which is a farcry from the style of most word processors (hjkl actually mean hjkl, not left down up right).

Word Wrap
---------
So I was excited to be able to write these blog posts using Vim since I've gotten quite good and fast at using it. However, I quickly realized that a text editor that primarily displays lines of code needs a bit of work to properly display words and sentences.

For instance, here's what I was seeing everytime I wrote a paragraph of text:
```text
This is a medium paragraph of text. Everything is all fine and dandy until you
 get to the end of the line. Then, whatever text you just wrote gets cut off p
recisely when you reach the column width of your Vim window. This leads to awk
ward and hard to read paragraphs!
```
So, after a bit of research, I threw this into my `.vimrc` file.
```vim
" F5 word wrap mode (great for writing blog posts!)
set wrap
nnoremap <F5> :set linebreak<CR>
nnoremap <C-F5> :set nolinebreak<CR>
```
The first line turns the `wrap` option on. The second line binds `:set linebreak<CR>` to `F5` in normal mode. The last line binds the reverse to `Ctrl` + `F5`. `<CR>` is a carriage return, otherwise known as the `enter` or `return` button.

So hitting `F5` in normal mode effectively types out `:set linebreak` then hits `enter` for you, rendering much more readable text for when you are not just writing code!
```text
This is a medium paragraph of text. Everything is all fine and dandy until
you get to the end of the line. Then, whatever text you just wrote gets cut
off precisely when you reach the column width of your Vim window. This
leads to awkward and hard to read paragraphs!
```

<span id="note">For those of you that are curious, Vim will automatically break the line as soon as it encounters any of these characters `^I !@*-+;:,./?`. You can change that default by redefining the `breakat` option. See [here](http://vim.wikia.com/wiki/Word_wrap_without_line_breaks) for details.</span>
