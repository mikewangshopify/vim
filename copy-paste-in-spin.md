# How to enable copy/paste in spin

## Some facts and concepts
* This text mainly talks about using vim in spin, not neovim in spin, and not vim in local Mac.
* Spin is a VPS(Virtual Private Server) with neither display device connected nor X server installed.
* Clipboard feature is not enabled by default in the vim version on spin, which can be checked with  `vim --version | grep clipboard`.
* Spin is an Ubuntu Linux distro. However, shopify has implemented command `pbcopy` in spin, which tunnels the copied text to the local Mac's clipboard. See details through `cat $(which pbcopy)` in spin.
* There are two kinds of copy/paste use cases in the context of vim. One use case is to copy/paste across vim editors in several tmux panes or windows. The other one is to copy text from vim and paste it in an text editor on the local Mac. A more strict saying of the second use case is to copy text from vim and paste it in anywhere that other than vim (in another word, where p does not work), e.g. command line of the spin shell.
* Copy text from local Mac to vim is not a problem. Just use CMD+c, CMD+v.

## How to copy/paste across tmux panes or windows
* Install `vim-gtk3` package to enable clipboard of vim
* Install `xvfb` package to get an [xvfb server](https://www.x.org/releases/X11R7.6/doc/man/man1/Xvfb.1.xhtml)
* Start the xvfb server as a background job, e.g. `nohup Xvfb :99 &`
* Define an alias `alias vim='DISPLAY=:99 vim'` to ensure vim is connected with the xvfb server
* `set clipboard=unnamed` in the `~/.vimrc` file to set the default registry 
* Then you should be able to copy/paste across tmux panes or windows using the vim native copy/paste keys, i.e. v to select lines, press y to copy, and press p in another pane or window to paste

## How to copy text from vim in spin and paste it in an editor on local Mac
* Add this line to your `~/.vimrc` file   
`map <C-c> y:e ~/clipsongzboard<CR>P:w !pbcopy<CR><CR>:bdelete!<CR>`
* Then you should be able use CTRL+c to copy the text selected, and then paste it to your local Mac using CMD + v.
* This vim command creates a buffer for a file named `~/clipsonyboard`, copy and paste the selected text into thisbuffer, then copy them further into the pbcopy clipboard from the buffer, and finally delete this buffer. The whole command is mapped with the shortcut CTRL+c.
* It looks a little bit complex and you may wonder why a buffer is needed and why not directly copy the text into pbcopy using `:w !pbcopy`. The reason is `:w !pbcopy` will copy the entire lines of the selected text. With the buffer operation, it is able to handle the block selection.

## How to copy text from Neovim in spin and paste it in an editor on local Mac 
* Add this line to your Neovim config file `init.vim`  
`let g:clipboard = {'copy': {'+': 'pbcopy', '*': 'pbcopy'}, 'paste': {'+': 'pbpaste', '*': 'pbpaste'}, 'name': 'pbc    opy', 'cache_enabled': 1}`
