# Budget Development

A simpler version is here:
https://medium.com/@peaceful_james/python-javascript-development-on-a-raspberry-pi-4-model-b-d778aa2bbb

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

Note that `asdf` can and should be used to install a _lot_ of things (elixir, erlang, postgres, sqlite, etc.)

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
$ pip install pylint jedi flake8 isort yapf mypy importmagic epc  # these libs make spacemacs work well
$ pip install pytest ipython pyscaffold selenium numpy pandas matplotlib # this is a random set of popular libs
$ putup my-first-backend # uses pyscaffold
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
   '(git
     helm
     multiple-cursors
     treemacs
     emacs-lisp
     (auto-completion :variables company-idle-delay 0.1
                      auto-completion-enable-sort-by-usage t
                      auto-completion-enable-snippets-in-popup t
                      auto-completion-tab-key-behavior 'cycle
                      :disabled-for org)
     (python :variables
             python-test-runner 'pytest
             python-backend 'anaconda  ;; the default backend, lsp, is very bad atm
             python-indent-offset 4
             ;; python-format-on-save t
             ;; python-sort-imports-on-save t
             )
     html
     (javascript :variables js-indent-level 2)
     react
     tide
     typescript
     markdown
     csv
     ;; lsp
     ;; better-defaults
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

and set scroll margin to 10 lines in the `user-config` section:

```
  (setq-default scroll-margin 10)
```

and enable flycheck globally and use pylint and mypy as flycheck "checkers" in the `user-config` section:

```
  (global-flycheck-mode 1)
  (flycheck-add-next-checker 'python-pylint 'python-mypy t)
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
    cd ~/.oh-my-zsh && git fetch --all && git pull
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

##### PART 16 (OPTIONAL) - postgres

```
sudo apt install libpq-dev
asdf plugin-add postgres
asdf install postgres 12.3  # or whatever version you want
asdf global postgres 12.3
pyenv shell my-first-backend
pip install psycopg2 pgcli   # now use pgcli instead of psql
```


##### PART 17 (OPTIONAL) - configure pylint

Generate a global pylint config file like this:

```
pylint --generate-rcfile > ~/.pylintrc
```

Edit that file and find the `disabled` list under the `[MESSAGES CONTROL]` section. Add this line to the list:

```
        missing-docstring
```

Don't forget to put a comma after the previous line! This will ignore warnings about missing docstrings, which happens all the time.

You can add pylint plugins, e.g. for django projects (django has sloppy AF typing):

```
load-plugins=pylint_django
```


##### PART 18 (OPTIONAL) - configure mypy

Generate a global mypy config file like this:

```
touch ~/.mypy.ini
```

Edit that file and put in these contents (for example):

```
# Global options:

[mypy]
warn_return_any = True
warn_unused_configs = True

# Per-module options:

[mypy-django.db]
ignore_missing_imports = True
```

See these links for more info:
https://mypy.readthedocs.io/en/stable/config_file.html#config-file
https://mypy.readthedocs.io/en/stable/running_mypy.html#missing-type-hints-for-third-party-library



##### PART 19 - erlang, elixir, phoenix

```
$ cd ~/.oh-my-zsh/custom/plugins
$ git clone https://github.com/gusaiani/elixir-oh-my-zsh.git elixir
$ cd
```

Add `elixir` to `~/.zshrc` plugins.

```
$ . ~/.zshrc
$ sudo apt install automake autoconf libncurses5-dev 
$ asdf plugin-add erlang
$ asdf plugin-add elixir
$ asdf list-all erlang
$ asdf list-all elixir
$ asdf install erlang 23.0.2
$ asdf install elixir 23.0.2
$ gcl https://github.com/elixir-lsp/elixir-ls.git
$ cd elixir-ls
$ asdf local erlang 23.0.2
$ asdf local elixir 1.10.3
$ mix compile
$ mix elixir_ls.release -o release
```

Add `dap` and `elixir` to the spacemacs layers:

```
     dap
     (elixir :variables 
          elixir-backend 'lsp
             elixir-ls-path "~/elixir-ls/release")
```

Add this stuff to the `.spacemacs` file in the `user-config` section:

```
;; LSP STUFF

  (setq lsp-file-watch-ignored
        '(".idea" ".ensime_cache" ".eunit" "node_modules"
          ".git" ".hg" ".fslckout" "_FOSSIL_"
          ".bzr" "_darcs" ".tox" ".svn" ".stack-work"
          "build" "_build" "deps" "postgres-data")
        )

  (use-package lsp-mode
    :commands lsp
    :ensure t
    :diminish lsp-mode

  (use-package lsp-ui
    :commands lsp-ui-mode
    :custom-face
    (lsp-ui-doc-background ((t (:background nil))))
    (lsp-ui-doc-header ((t (:inherit (font-lock-string-face italic)))))
    :bind (:map lsp-ui-mode-map
                ([remap xref-find-definitions] . lsp-ui-peek-find-definitions)
                ([remap xref-find-references] . lsp-ui-peek-find-references)
                ("C-c u" . lsp-ui-imenu))
    :init
    (setq lsp-ui-doc-enable nil
          lsp-ui-doc-header t
          lsp-ui-doc-include-signature t
          lsp-ui-doc-position 'bottom
          lsp-ui-doc-use-webkit t
          ;; lsp-ui-doc-border (face-foreground 'default)

          lsp-ui-sideline-enable nil
          lsp-ui-sideline-ignore-duplicate t)
    :config
    ;; WORKAROUND Hide mode-line of the lsp-ui-imenu buffer
    ;; https://github.com/emacs-lsp/lsp-ui/issues/243
    (defadvice lsp-ui-imenu (after hide-lsp-ui-imenu-mode-line activate)
      (setq mode-line-format nil)))

  (use-package company-lsp
    :init (setq company-lsp-cache-candidates 'auto)
    :config (push 'company-lsp company-backends)
    )
  ;; this doesn't work:
  ;; (add-hook 'projectile-after-switch-project-hook #'lsp-restart-workspace)

  (require 'dap-elixir)
  (dap-ui-mode)
  (dap-mode)
  (with-eval-after-load 'elixir-mode
    (spacemacs/declare-prefix-for-mode 'elixir-mode
      "mt" "tests" "testing related functionality")
    (spacemacs/set-leader-keys-for-major-mode 'elixir-mode
      "j=" 'lsp-format-buffer
      "tb" 'exunit-verify-all
      "ta" 'exunit-verify
      "tk" 'exunit-rerun
      "tt" 'exunit-verify-single))

  ;; kbd macro for IEx.pry - press Space, m ,d to put in a breakpoint (like in python-mode).
  (fset 'iex_pry
        (lambda (&optional arg) "Keyboard macro." (interactive "p") (kmacro-exec-ring-item (quote ([111 114 101 113 117 105 114 101 32 73 69 120 return 73 69 120 46 112 114 121 escape] 0 "%d")) arg)))
```


Add this elixir goodness to `~/.zshrc` :

```
export ERL_AFLAGS="-kernel shell_history enabled"

alias mto='mix test --only $@'

function parse_mix_hex_search_line() {
    local line="$1";
    local arr=("${(s/ /)line}")
    echo "{:${arr[1]}, \"~> ${arr[$((#arr[@] - 2))]}\"},"
}

function mix_hex_search_formatted_output() {
    mix hex.search $1 | grep $1  | while read line; do
        parse_mix_hex_search_line $line
    done
}
```

and make a default `.iex.exs` for pleasant iex shell:

```
# from http://samuelmullen.com/articles/customizing_elixirs_iex/
# you need to save this file as ~/.iex.exs

IEx.configure(
  colors: [
    syntax_colors: [
      number: :light_yellow,
      atom: :light_cyan,
      string: :light_black,
      boolean: :red,
      nil: [:magenta, :bright],
    ],
    ls_directory: :cyan,
    ls_device: :yellow,
    doc_code: :green,
    doc_inline_code: :magenta,
    doc_headings: [:cyan, :underline],
    doc_title: [:cyan, :bright, :underline],
  ],
  default_prompt:
  "#{IO.ANSI.green}%prefix#{IO.ANSI.reset} " <>
    "[#{IO.ANSI.magenta}#{IO.ANSI.reset}" <>
    "#{IO.ANSI.cyan}%counter#{IO.ANSI.reset}] >",
  alive_prompt:
  "#{IO.ANSI.green}%prefix#{IO.ANSI.reset} " <>
    "(#{IO.ANSI.yellow}%node#{IO.ANSI.reset}) " <>
    "[#{IO.ANSI.magenta}#{IO.ANSI.reset}" <>
    "#{IO.ANSI.cyan}%counter#{IO.ANSI.reset}] >",
  history_size: 500,
  inspect: [
    pretty: true,
    limit: :infinity,
    width: 80
  ],
  width: 80
)


defmodule CustomRootModuleNoMappingCollision do
  # This is just to illustrate how you can have custom functions/aliases
  # THis is particularly useful when done per-project
  def timestamp do
    {_date, {hour, minute, _second}} = :calendar.local_time
    [hour, minute]
    |> Enum.map(&(String.pad_leading(Integer.to_string(&1), 2, "0")))
    |> Enum.join(":")
  end
end

alias CustomRootModuleNoMappingCollision, as: Hyu
```


##### PART 20 - Git config

The first time you try using git on a new machine, it will ask who you are. In particular, it will ask you to provide a name and an email. When you give it this information, it puts it in `~/.gitconfig`. This is not the right set-up for people who have multiple git accounts, e.g. a github account and a gitlab account, or a personal account and a work acount. You might use different emails for different git accounts so here is a `~/.gitconfig` to solve that:

```
[includeIf "gitdir:~/github-personal/"]
	path = ~/github-personal/.gitconfig

[includeIf "gitdir:~/github-work/"]
	path = ~/github-work/.gitconfig

[includeIf "gitdir:~/gitlab/"]
	path = ~/gitlab/.gitconfig
```

What is happening here is we have told git about 3 different directories, each containing their own `.gitconfig` file.

So you would just make those directories and their `.gitconfig` files:

```
$ mkdir ~/github-personal
$ mkdir ~/github-work
$ mkdir ~/gitlab
$ touch ~/github-personal/.gitconfig
$ touch ~/github-work/.gitconfig
$ touch ~/gitlab/.gitconfig
```

and write a separate config for each directory, e.g., for the `github-personal` one:

```
[user]
        email = chilling@home.fakemail
        name = Tres Chill
```

and for the `github-work` one:

```
[user]
        email = serious@work.moneymoneymoney
        name = Mr Professional
```

which is super nice because it means that when you are not in one of these 3 directories, e.g. you are just in your home folder, and you pull some repository to play with it, that pull won't be done using any name or email.

Note that if you are going with a set-up like this then you should have a separate SSH key for each git account. For example, let's create an ssh key just for our "work" github account:

```
$ ssh-keygen -t ed25519 -C "github-work" -f ~/.ssh/id_github-work
```

It will ask for a passphrase to be entered twice. Use a good one.
You would then just log in to github in the browser, find the security/keys settings and add the _public_ key. This should be `~/.ssh/id_github-work.pub`.

Using separate git configs (with no global git config) and separate, clearly-named ssh keys is good.

Finally, you might want to have a "global gitignore". You might know that most git projects have a `.gitignore` file, which lists directories and file-name patterns that git should ignore. For example, a common one is `node_modules`, the directory that `npm install` creates to hold _all_ of the node libraries a project uses. Since this is usually very large, and changes often, it is wasteful and pointless to commit and push `node_modules`, since anyone can generate their own with `npm install`. You can tell git to use a "global gitignore" file which will apply everywhere.

For example, if you are using `pyenv` and you pull a python repository written by a team who don't use pyenv, then when you create and assign a venv for that project, git will see the `.python-version` file as an untracked file. You could add `.python-version` to the project's gitignore but you're still imposing your own tools onto a pre-existing project/team. It is generally more polite to globally gitignore your own tools' files.

Configure the global gitignore path like this:
```
$ git config --global core.excludesfile '~/.gitignore-global'
```

Notice the correct name  for a global gitignore is `core.excludesfile`.

Note that you can call that file whatever you want. I like to call it `.gitignore-global`. Here's a simple example:

```
# Configure git to use this with:
# $ git config --global core.excludesfile '~/.gitignore-global'

# The pyenv python-version file
.python-version

# LSP dialyzer-generated directory
.elixir_ls

# projectile file used by projectile/emacs
.projectile
```

WIth this setup, I can make a `.projectile` file in any project, or run `pyenv virtualenv 2.7-dev new-team-project; pyenv local new-team-project` to make a `.python-version` file in any project and git will ignore it. Very clean and polite.
