# TMux

- *Prefix* + **:**: Go into **command mode**

### Pane management
- *Prefix* + **|**: Horizontal split
- *Prefix* + **-**: Vertical split
- *Prefix* + **←↑→↓**: Move focus
- *Prefix* + **z**: Toggle zoom
- *Prefix* + **q**: Display pane numbers
- *CommandMode* **resize-pane -D NUMBER**: Resize pane downwards
- *CommandMode* **resize-pane -U NUMBER**: Resize pane upwards
- *CommandMode* **resize-pane -L NUMBER**: Resize pane leftwards
- *CommandMode* **resize-pane -R NUMBER**: Resize pane rightwards

### Window management
- *Prefix* + **w**: Window list
- *Prefix* + **c**: Create new window
- *Prefix* + **n**: Cycle to next window
- *Prefix* + **p**: Cycle to previous window

### Session management
- *Prefix* + **d**: Detach from session
- ``tmux attach``: Attach to most recently used session
- *Prefix* + **s**: List active sessions
- ``tmux ls``: List active sessions
- ``tmux attach -t NAME``: Attach to session
- *Prefix* + **$**: Rename a session
- ``tmux rename-session -t OLDNAME NEWNAME``: Rename a session
- ``tmux new -s NAME``: Open a new named session
- ``tmux kill-session -t NAME``: Kill session

### Config
- Edit ``~/.tmux.conf``
- *CommandMode* ``source-file ~/.tmux.conf``: Reload config
- ``unbind COMBO``: Unbind a key combo
- ``set-option -g``: Set a global option
- ``set-option -g prefix COMBO``: Bind **prefix** to a new key combo
- ``bind-key C-a send-prefix``: Bind **prefix** to *Ctrl+a*
- ``set -g status-bg blue``: Change status bar background color
- ``set -g status-fg white``: Change status bar foreground color
- ``bind R source-file ~/.tmux.conf``: Bind *prefix* **Sh + r** to Reload config
- ``set-window-option -g mode-keys vi``: Vi key mode in **copy mode**
- ``bind m set -g mouse on``
- ``bind M set -g mouse off``

### Copy & Paste
- *Prefix* + **[**: Go into **copy mode**
- *CopyMode* **q**: Leave **copy mode**
- *CopyMode* **b**: Previous word
- *CopyMode* **e**: Next word
- *CopyMode* **space**: Start mark
- *CopyMode* **enter**: Copy marked text
- *Prefix* + **]**: Paste

### Exmaple startup script
```shell
SESSION=dev
tmux new-session -d $SESSION
tmux new-window -t $SESSION:1 -n 'webserver'

tmux select-window -T $SESSION:1
tmux split-window -h
tmux send-keys 'cd examples/react; python -m SimpleHTTPServer' C-m

tmux attach -t $SESSION
```
