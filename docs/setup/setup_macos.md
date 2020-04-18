
## set up my new mac pro

old post https://divingintogeneticsandgenomics.rbind.io/post/set-up-my-new-mac-laptop/

### download [iterm](https://www.iterm2.com/)

configure color
iTerm → Preferences → Profiles → colors -> Color Presets --> Tango Dark

By default, word jumps (option + → or ←) and word deletions (option + backspace) do not work. To enable these, go to "iTerm → Preferences → Profiles → Keys → Load Preset... → Natural Text Editing → Boom! Head explodes"

### iterm2 tips 

* press command + click the file inside the terminal to open it!

### install oh-my-zsh

```bash
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### zsh syntax highlighting

https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md

`Oh-my-zsh`:
Clone this repository in oh-my-zsh's plugins directory:

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
Activate the plugin in ~/.zshrc:
```

my `.zshrc` has a plugin `git`, add it after that 

```
plugins=(git zsh-syntax-highlighting)

```
https://github.com/zsh-users/zsh-syntax-highlighting/issues/530
#### shell integration

https://iterm2.com/documentation-shell-integration.html

logout and then login

#### view images inside terminal
put [`imgcat`](https://www.iterm2.com/utilities/imgcat) to your `~/bin`.
and add `export PATH=$PATH:~/bin` to your `.zshrc`
https://www.iterm2.com/documentation-images.html



A note on login shell and interactive shell.
https://codingbee.net/rhcsa/rhcsa-starting-a-login-shell-or-interactive-shell-using-the-switch-user-su-command

### install conda

```bash
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
```

### install sublime, rmate
### install R and Rstudio

