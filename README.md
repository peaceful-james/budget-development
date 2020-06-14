# Budget Development

### Guide to setting up python + javascript development environment on a raspberry pi (raspbian, but should work with other OS, e.g. lubuntu, kali).

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
$ sudo apt install tk-dev chromedriver libreoffice-draw   # optional stuff that is often good to have
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

##### PART 6 - get used to pyenv

Example choosing global (default) python version:

```
$ pyenv install 3.8-dev   # installs python-3.8
$ pyenv global 3.8-dev    # sets default python version to 3.8
$ pip install --upgrade pip     # not necessary but nice to do
```

Example creating venv using pyenv and using pyscaffold:

```
$ cd
$ mkdir my-first-project
$ cd my-first-project
$ pyenv virtualenv 3.8-dev my-first-backend
$ pyenv local my-first-backend
$ pip install ipython mypy pyscaffold selenium numpy   # this is a random set of popular libs
$ putup my-first-backend
$ cd my-first-backend 
$ pip install -r requirements.txt
```

##### PART 7 - get used to asdf

```
$ asdf install nodejs 14.4.0
$ asdf global nodejs 14.4.0
$ npm install -g expo expo-cli typescript tslint tern eslint babel-eslint eslint-plugin-react js-beautify prettier
$ cd ~/my-first-project
$ npx create-react-app my-first-frontend
```

##### PART 8 - using spacemacs:

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
     (javascript :variables js-indent-level 2)
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

and disable lockfile creation. This is optional but if you don't do it then you will see errors when, e.g. running a node project, since the file watcher will try to use the "lockfiles" which are ephemeral (lockfiles are auto-save files).
So add this line to the `user-config` section:

```
  (setq create-lockfiles nil)
```

This 

Save the config file with `SPACE, f, s` and quit emacs with `SPACE, q, Q`.

##### PART 9 - make spacemacs fast and easy

For some understanding, you can run emacs in daemon mode, where it has a server and client. The client command is `emacsclient`. This `emacsclient` command has a `-a` flag which will start the server if it doesn't exist.

So here is how to use spacemacs most efficiently:

Edit the `~/.spacemacs` file (or press `SPC, f, e, d` to edit it in spacemacs).
Change this line:

```
   dotspacemacs-persistent-server nil
```

to this:

```
   dotspacemacs-persistent-server t
```

Change this line:

```
   dotspacemacs-enable-server nil
```

to this:

```
   dotspacemacs-enable-server t
```

And finally add this line to the `user-config` section:

```
  (evil-leader/set-key "q q" 'spacemacs/frame-killer)
```

Now quit emacs `SPC, q, Q` and put two new binaries in `/bin` or `/usr/bin` or wherever you like:

```
sudo vi /bin/em
```

Just put in this line:

```
emacsclient -a '' -nw $@
```

and similarly make a second bin file:

```
sudo vi /bin/emw
```

Just put in this line:

```
emacsclient -a '' -nc $@
```

Make both of these executable:

```
sudo chmod +x /bin/em
sudo chmod +x /bin/emw
```

From now on, if you want to open emacs, use `emw` and `em`.

For example, to edit a file in the proper emacs app, you would do:

```
emw ~/work/project_42/src/setup.py
```

and to edit a file headlessly (in your terminal), you would do:

```
em ~/.zshrc
```

These will open super fast because the emacs server is constantly running as a daemon (a.k.a background process).

In both ways, you close the "frame" without killing emacs by using the `frame-killer` command which we mapped to `SPC, q, q` (by default it is `SPC, F, d` which is annoying to remember and type).

You can still kill emacs fully using the same `SPC, q, Q` we used above.

If this all works OK for you and you like it, you can go a step further and put this in your .zshrc to make `em` the default editor (e.g. for editing `git commit` messages):

```
export EDITOR=em
```

Disclaimer: headless spacemacs can look kind of bad, especially in modes with lots of colour formatting, e.g. markdown-mode.

##### PART 10 - using screen

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

##### PART 11 - know how to update everything

It is wise and efficient to make your own update_everything function in your .zshrc.
For example:

```
function update_everything() {
    sudo apt update -y
    sudo apt upgrade -y
    cd ~/.pyenv && git fetch --all && git pull
    cd ~/.pyenv/plugins/pyenv-virtualenv && git fetch --all && git pull
    cd ~/.asdf && git fetch --all && git pull
    cd ~/.emacs.d && git fetch --all && git pull
    asdf plugin-update --all
    cd
}
```

##### PART 12 (OPTIONAL) - useful alias for opening things from terminal
This alias is handy for opening things from the terminal.

```
$ vi ~/.zshrc
```
add this at the end:

```
alias o="xdg-open $@"
```

The `$@` is a special variable which means "all the arguments".
Similarly, `$1` is the first argument, `$2` is the second, etc.

You can make your own aliases as you wish. Here are some of mine:

```
alias u="update_everything"
alias s="screen -dRR"
alias b="chromium-browser $@"
alias lock="/usr/bin/dm-tool lock"   # locks the screen
alias beep="aplay /usr/lib/libreoffice/share/gallery/sounds/apert.wav 2>/dev/null"   # makes a nice sound.
```

I use that beep alias after long-running commands, so I know they're finished, e.g. `pip install matplotlib; beep`. The `2>/dev/null` just pipes error output to /dev/null which is effectively a black hole where bits go to stop existing.

##### PART 13 (OPTIONAL)- hide the ugly "decoration" bar at the top of windows (get it back using alt+space, d):

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

##### PART 14 (OPTIONAL) - hide the ugly menu bars from window

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


##### PART 15 (OPTIONAL) - force hdmi detection after boot

In default setup, the Pi only detects HDMI if plugged in before boot.
Let HDMI work after boot by doing:

```
sudo vi /boot/config.txt
```

and uncomment (remove the `#` symbol at the start of the line) this:

```
hdmi_force_hotplug=1
```
