# Budget Development

### Guide to setting up do python + JS development on a raspberry pi (raspbian, but should work with other OS, e.g. lubuntu, kali).

I did all this on a Raspberry Pi 4, Model B, with a 32GB SD card.

At time of completion, I was able to run a node project (using expo) and a python backend.
Final disk usage is under 7GB.

### Checklist
- shell: zsh
- python version management: pyenv
- node version management: asdf
- editor: spacemacs

### Prerequisites
You should know:
1. how to use vi http://yannesposito.com/Scratch/en/blog/Learn-Vim-Progressively/
2. ctrl+alt+t opens the terminal

### Setup

##### PART 0 - the basics

Open your terminal and run these things (but don't include the $ at the start of every line):

```
$ sudo apt update -y
$ sudo apt upgrade -y
$ sudo apt install gcc make curl libffi-dev pkg-config libz-dev libbz2-dev libsqlite3-dev libncurses-dev libexpat1-dev libssl-dev libgdbm-dev libgc-dev liblzma-dev bzip2 openssl zlib1g-dev libreadline-dev
$ sudo apt install dirmngr emacs zsh screen ripgrep git
```

##### PART 1 - the shell (oh-my-zsh)
```
$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

```
$ vi ~/.zshrc
```

Edit the zshrc to include the git plugin:

```
plugins=(git)
```
and set the theme to "semaphore":
```
ZSH_THEME="semaphore"
```

Then add this semaphore theme by writing this content to ~/.oh-my-zsh/custom/themes/semaphore.zsh-theme

```
$ vi ~/.oh-my-zsh/custom/themes/semaphore.zsh-theme
```

Just add all this:

```
# Based on the CRUNCH theme
# Put this in ~/.oh-my-zsh/custom/themes dir

NEWLINE=$'\n'

# Colors
SEMAPHORE_DEFAULT_COLOR="%{$fg[white]%}"
SEMAPHORE_NVM_COLOR="%{$fg[magenta]%}"
SEMAPHORE_DIR_COLOR="%{$fg[cyan]%}"
SEMAPHORE_GIT_BRANCH_COLOR="%{$fg[green]%}"
SEMAPHORE_GIT_CLEAN_COLOR="%{$fg[green]%}"
SEMAPHORE_GIT_DIRTY_COLOR="%{$fg[red]%}"
SEMAPHORE_TIME_COLOR="%{$fg[magenta]%}"
SEMAPHORE_PRP_COLOR="%{$fg[magenta]%}"

# These Git variables are used by the oh-my-zsh git_prompt_info helper:
ZSH_THEME_GIT_PROMPT_PREFIX="$SEMAPHORE_DEFAULT_COLOR:$SEMAPHORE_GIT_BRANCH_COLOR"
ZSH_THEME_GIT_PROMPT_SUFFIX=""
ZSH_THEME_GIT_PROMPT_CLEAN=" $SEMAPHORE_GIT_CLEAN_COLOR✓$SEMAPHORE_DEFAULT_COLOR"
ZSH_THEME_GIT_PROMPT_DIRTY=" $SEMAPHORE_GIT_DIRTY_COLOR✗$SEMAPHORE_DEFAULT_COLOR"

# the left hand side prompt
SEMAPHORE_LHS="$SEMAPHORE_DIR_COLOR%~\$(git_prompt_info)"
# newline at the end for ease of reading commands
SEMAPHORE_NEWLINE_PROMPT="${NEWLINE}$SEMAPHORE_PRP_COLOR➭$SEMAPHORE_DEFAULT_COLOR "
# final multiline prompt
PROMPT="$SEMAPHORE_LHS$SEMAPHORE_NEWLINE_PROMPT"

#Customized git status, oh-my-zsh currently does not allow render dirty status before branch
git_custom_status() {
    local cb=$(git_current_branch)
    if [ -n "$cb" ]; then
        echo "%{$fg_bold[red]%}$(work_in_progress)%{$reset_color%}"
    fi
}

# 24-hour clock time with seconds
SEMAPHORE_TIME="%{$fg[magenta]%}%D{%K:%M:%S}$SEMAPHORE_DEFAULT_COLOR"

# Put node version (nvm) and time on right hand side prompt
#RPS1='$SEMAPHORE_NVM_COLOR [node-$(nvm_prompt_info)]$SEMAPHORE_DEFAULT_COLOR $(git_custom_status) $SEMAPHORE_TIME'
#RPS1='$(git_custom_status) $SEMAPHORE_TIME'
RPS1='$(git_custom_status) $SEMAPHORE_TIME'
```

# When this is done, restart the pi. Continue after restarting and opening new shell.

##### PART 2 - python + pyenv

```
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
$ git clone https://github.com/pyenv/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
```

##### PART 3 - asdf + node

```
$ git clone https://github.com/asdf-vm/asdf.git ~/.asdf
$ asdf plugin-add nodejs
```

##### PART 4 - spacemacs

```
$ git clone https://github.com/syl20bnr/spacemacs ~/.emacs.d
$ cd .emacs.d
$ git checkout develop
```

##### PART 5 - ensure shell knows about pyenv and asdf

```
$ vi ~/.zshrc
```

add this stuff at the end:

```
. $HOME/.asdf/asdf.sh
. $HOME/.asdf/completions/asdf.bash

export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

##### PART 6 (OPTIONAL) - alias for opening things from terminal
This alias is handy for opening things from the terminal.

```
$ vi ~/.zshrc
```
add this at the end:

```
alias o="xdg-open $@"
```

##### PART 7 (OPTIONAL)- hide the ugly "decoration" bar at the top of windows (get it back using alt+space, d):

```
$ vi ~/.config/openbox/lxde-pi-rc.xml
```

Here is all of mine, you just need the `<applications>` section

```
<?xml version="1.0"?>
<openbox_config>
    <theme>
        <font place="ActiveWindow">
            <name>PibotoLt
            </name>
            <size>12
            </size>
            <weight>Normal
            </weight>
            <slant>Normal
            </slant>
        </font>
        <font place="InactiveWindow">
            <name>PibotoLt
            </name>
            <size>12
            </size>
            <weight>Normal
            </weight>
            <slant>Normal
            </slant>
        </font>
        <invHandleWidth>10
        </invHandleWidth>
        <titleColor>#000000
        </titleColor>
        <textColor>#00f50e
        </textColor>
    </theme>
    <applications>  
        <application class="*">
            <maximized>true</maximized>
            <decor>no</decor>
        </application>
    </applications>
</openbox_config>
```

##### PART 8 (OPTIONAL) - hide the ugly menu bars from window

```
$ vi /home/pi/.config/lxterminal/lxterminal.conf
```

change this line:

```
hidemenubar=false
```

to this:

```
hidemenubar=true
```

(That line belongs in the `[general]` section.)

##### PART 9 - use spacemacs:

```
$ emacs
```

Your first time running this, it will take some time to set itself up.
Watch the bar at the bottom of emacs for prompts and info.
During setup, select "evil" settings and go for full-feature installation.
When it's ready, press space,f,e,d to open config file.

Change this line:

```
   dotspacemacs-maximized-at-startup nil
```

to this:

```
   dotspacemacs-maximized-at-startup t
```

and add some useful layers:

```
   ;; List of configuration layers to load.
   dotspacemacs-configuration-layers
   '(python
     javascript
     typescript
     tide
     html
     auto-completion
     emacs-lisp
     git
     helm
     multiple-cursors
     treemacs
     ;; better-defaults
     ;; lsp
     ;; markdown
     ;; org
     ;; (shell :variables
     ;;        shell-default-height 30
     ;;        shell-default-position 'bottom)
     ;; spell-checking
     ;; syntax-checking
     ;; version-control
     )
```

and change the font:

```
   ;; Default font or prioritized list of fonts.
   dotspacemacs-default-font '("Monospace"
                               :size 14.0
                               :weight normal
                               :width normal)
```

and enable line-numbers:

```
   dotspacemacs-line-numbers t
```

Save the config file with `SPACE, F, S` and quit emacs with `SPACE, q, Q`.

##### PART 10 - get used to pyenv

Example choosing global (default) python version:

```
$ pyenv install 3.8-dev   # installs python-3.8
$ pyenv global 3.8-dev    # sets default python version to 3.8
$ pip install --upgrade pip     # not necessary but nice to do
```

Example creating venv using pyenv and using pyscaffold:

```
$ mkdir hyu
$ cd hyu
$ pyenv virtualenv 3.8-dev hyu
$ pyenv local hyu
$ pip install ipython mypy pyscaffold
$ putup my_proj   # unfortunately this creates the project in a sub-directory
$ mv my_proj/* .  # move things out of the sub-dir
$ mv my_proj/.* .  # move things out of the sub-dir
$ rmdir my_proj   # remove the generated sub-dir
$ pip install -r requirements.txt
```

##### PART 11 - get used to asdf

```
$ asdf install nodejs 14.4.0
$ asdf global nodejs 14.4.0
$ npm install -g expo expo-cli typescript tslint
```

##### PART 12 - using screen

Overwrite ~/.screenrc with this content (remaps default ctrl+a to ctrl+space):

```
shell "/bin/zsh"

#from https://serverfault.com/questions/257975/how-to-check-if-im-in-screen-session
# caption always "%{= kc}Screen session on %H (system load: %l)%-28=%{= .m}%D %d.%m.%Y %0c"

#from https://lizdenys.com/journal/articles/understanding-gnu-screens-captions.html
# caption always '%{= kw}[ %{y}%H%{-} ][ %= %-Lw%{+b M}%n%f* %t%{-}%+LW %= ][ %{r}%l%{-} ][ %{c}%c%{-} ]'
# caption always '%{= kw}[ %= %-Lw%{+b M}%n%f* %t%{-}%+LW %= ][ %{r}%l%{-} ][ %{c}%c%{-} ]'

#https://serverfault.com/questions/3740/what-are-useful-screenrc-settings
# THIS IS THE PRETTY BIT
#change the hardstatus settings to give an window list at the bottom of the                                                                        
##screen, with the time and date and with the current window highlighted                                                                            
hardstatus             alwayslastline                                                                                                                          
#hardstatus string '%{= mK}%-Lw%{= KW}%50>%n%f* %t%{= mK}%+Lw%< %{= kG}%-=%D %d %M %Y %c:%s%{-}'
# hardstatus string '%{= kG}[ %{G}%H %{g}][%= %{= kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%?%= %{g}][%{B} %d/%m %{W}%c %{g}]'
hardstatus string '%{= kG}[%= %{= kw}%?%-Lw%?%{r}(%{W}%n*%f%t%?(%u)%?%{r})%{w}%?%+Lw%?%?%= %{g}][%{B} %d/%m %{W}%c %{g}]'

escape ^@@

defscrollback 100000

altscreen on

rendition so =06
rendition so km
caption string "%{03} "
```

I left in some commented-out config for the curious/brave. I don't know if it works.
You should run screen with this command:

```
screen -dRR
```

In screen, use these commands:

```
ctrl+space, ?    # help
ctrl+space, c    # creates a new window
ctrl+space, "    # lists all windows
ctrl+space, 4    # switch to window 4 (for example)
ctrl+space, s    # split horizontally
ctrl+space, |    # split vertically
ctrl+space, tab    # move to next window (when split)
ctrl+space, Q    # fullscreen current window
```
