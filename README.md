# Basics

Create a new file under `/etc/environment.d/` with `EDITOR=nvim` to set it as a default terminal editor.

Add `ctrl:nocaps` to the `XKBOPTIONS` in `/etc/default/keyboard` to remap CapsLock to Ctrl.

# Bash

Create a directory at `/etc/bash.d` and add the following to the `/etc/bash.bashrc` file.

```sh
if [ -d /etc/bash.d ]; then
  for i in /etc/bash.d/*.bashrc; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi
```

Then create the following files under the newly made directory.

**sane_defaults.bashrc**
```sh
stty -ixon # Disable terminal pause

set -o ignoreeof # Don't kill my session when I press Ctrl+D
set -P # Don't resolve symlinks, but follow the path instead.
```

**aliases.bashrc**
```sh
alias g="git"
alias e="$EDITOR"
alias se="sudo $EDITOR"
```

**prompt.bashrc**
```sh
function parse_git_branch() {
  BRANCH=`git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/'`
  if [ ! "${BRANCH}" == "" ]
  then
    STAT=`parse_git_dirty`
        echo "(${BRANCH}${STAT}) "
  else
    echo ""
  fi
}

function parse_git_dirty {
  status=`git status 2>&1 | tee`
  dirty=`echo -n "${status}" 2> /dev/null | grep "modified:" &> /dev/null; echo "$?"`
  untracked=`echo -n "${status}" 2> /dev/null | grep "Untracked files" &> /dev/null; echo "$?"`
  ahead=`echo -n "${status}" 2> /dev/null | grep "Your branch is ahead of" &> /dev/null; echo "$?"`
  newfile=`echo -n "${status}" 2> /dev/null | grep "new file:" &> /dev/null; echo "$?"`
  renamed=`echo -n "${status}" 2> /dev/null | grep "renamed:" &> /dev/null; echo "$?"`
  deleted=`echo -n "${status}" 2> /dev/null | grep "deleted:" &> /dev/null; echo "$?"`
  bits=''
  if [ "${renamed}" == "0" ]; then
    bits=">${bits}"
  fi
  if [ "${ahead}" == "0" ]; then
    bits="*${bits}"
  fi
  if [ "${newfile}" == "0" ]; then
    bits="+${bits}"
  fi
  if [ "${untracked}" == "0" ]; then
    bits="?${bits}"
  fi
  if [ "${deleted}" == "0" ]; then
    bits="x${bits}"
  fi
  if [ "${dirty}" == "0" ]; then
    bits="!${bits}"
  fi
  if [ ! "${bits}" == "" ]; then
    echo " ${bits}"
  else
    echo ""
  fi
}

export PS1="[\t] \w \`parse_git_branch\`:: "
```

# Git

Create these new files at `/etc`.

**/etc/gitconfig**
```ini
[user]
  name = "Dawid Wolosowicz"
[alias]
  sdiff = diff --staged
  unstage = "reset HEAD"
  rollback = "checkout --"
  reset-author-globally = filter-branch --commit-filter 'export GIT_COMMITER_NAME=\"$(git config --get user.name)\" && export GIT_COMMITER_EMAIL="$(git config --get user.email)" && export GIT_AUTHOR_NAME=\"$(git config --get user.name)\" && export GIT_AUTHOR_EMAIL="$(git config --get user.email)" && git commit-tree "$@"'
  slog = log --format=oneline
[core]
  excludesfile = /etc/gitexcludesfile
  editor = "nvim"
  commentchar = "*"
```

**/etc/gitexcludesfile**
```
.idea/
.DS_Store
*.swp
```

**~/.config/git/config**
```ini
[user]
  email = "<appropriate-email-for-the-user>"
```

# Docker

Reference https://docs.docker.com/engine/security/rootless/ to set it up Docker as rootless installation.

# IdeaVim

Add a new file at `~/.config/idea`.

**ideavimrc**
```
set surround
set commentary

set idearefactormode="keep"
set ideajoin

set clipboard+=unnamed

nno gd :action GotoDeclaration<cr>
nno gb :action Back<cr>
nno gf :action Forward<cr>

nno <C-h> <C-w>h
nno <C-j> <C-w>j
nno <C-k> <C-w>k
nno <C-l> <C-w>l
nno <C-w>q <C-w>c
nno == :action ReformatCode<cr>

nno U :redo<CR>
vno > >gv
vno < <gv
```
