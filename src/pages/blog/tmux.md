---
layout: ../../layouts/BlogLayout.astro
title: My Productive Tmux Setup
description: Speed, Navigation, and Eye Candy
tags: ["tmux", "workflow", "terminal"]
time: 5
featured: true
timestamp: 2025-08-15T18:39:03+00:00
filename: tmux
---

![Tmux](/assets/tmux.png)

There’s no shortage of guides on how to master Tmux and make it shine - so
instead of rehashing everything, I’ll focus on the essentials that work best for
me.

## 🛠 Plugin Powerhouse

I use TPM (Tmux Plugin Manager) to install and manage plugins. My must-haves:

- tmux-sensible - A set of sane defaults so I don’t have to reinvent the wheel.
- vim-tmux-navigator – Seamless pane navigation between tmux and Vim with hjkl.
- tmux-yank – Easy copy to system clipboard.
- catppuccin/tmux – Beautiful themed status bar and window styling.

## 🎨 True Color & Terminal Tweaks

This makes a big difference when using themes like Catppuccin.

```tmux
set -g default-terminal "screen-256color"
set-option -sa terminal-features ',xterm-*:RGB'
set-option -ga terminal-overrides ',xterm-*:Tc'
```

## ⚡ Fast, No-Nonsense Workflow

- Kill panes instantly:

```tmux
bind-key x kill-pane # removes the confirmation prompt.
```

- Stay in tmux after closing a session:

```tmux
set -g detach-on-destroy off
```

- Reload config instantly with prefix + r:

```tmux
bind-key r source-file ~/.tmux.conf \; display-message "~/.tmux.conf reloaded."
```

## 🪟 Splits & Navigation

- Horizontal split: v
- Vertical split: b
- hjkl for Vim-style pane navigation.
- Alt + Arrow keys to switch panes without the tmux prefix.
- Shift + Arrow or Shift + Alt + H/L to switch windows.

I also start panes/windows at 1 instead of 0:

```tmux
set -g base-index 1
set -g pane-base-index 1
```

## 🖱 Mouse & Clipboard Integration (macOS)

Mouse mode makes resizing and selecting easier:

```tmux
set -g mouse on
```

For macOS clipboard sync:

```tmux
bind-key C-c run-shell 'tmux save-buffer - | reattach-to-user-namespace pbcopy'
bind-key C-v run-shell 'reattach-to-user-namespace pbpaste | tmux load-buffer - \; paste-buffer -d'
```

## 📂 Same-Directory Splits

New splits open in the current pane’s working directory:

```tmux
bind '"' split-window -v -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"
```

## ⚡ Session Switching with Style

[Sesh](https://github.com/joshmedeski/sesh) is my secret weapon for jumping
between Tmux sessions, configs, and directories with fuzzy search. With one
keybinding, I get a beautiful popup with all my workspaces:

![Sesh](/assets/sesh.png)

```tmux
# Sesh popup bindings
bind-key "o" run-shell "sesh connect \"$(
  sesh list --icons | fzf-tmux -p 55%,60% \
    --no-sort --ansi --border-label ' sesh ' --prompt '⚡  ' \
    --header '  ^a all ^t tmux ^g configs ^x zoxide ^d tmux kill ^f find' \
    --bind 'tab:down,btab:up' \
    --bind 'ctrl-a:change-prompt(⚡  )+reload(sesh list --icons)' \
    --bind 'ctrl-t:change-prompt(🪟  )+reload(sesh list -t --icons)' \
    --bind 'ctrl-g:change-prompt(⚙️  )+reload(sesh list -c --icons)' \
    --bind 'ctrl-x:change-prompt(📁  )+reload(sesh list -z --icons)' \
    --bind 'ctrl-f:change-prompt(🔎  )+reload(fd -H -d 2 -t d -E .Trash . ~)' \
    --bind 'ctrl-d:execute(tmux kill-session -t {2..})+change-prompt(⚡  )+reload(sesh list --icons)' \
)\""
```

Here’s how it works:

- Press Ctrl+B and o → Opens fzf-tmux in a popup (55% width x 60% height).
- Use Tab/Shift+Tab to navigate.
- Switch categories with shortcuts:
  - Ctrl+A → All sessions
  - Ctrl+T → Tmux sessions
  - Ctrl+G → Configs
  - Ctrl+X → Zoxide bookmarks
  - Ctrl+F → Directory search with fd
  - Ctrl+D → Kill tmux sessions directly from the menu

This means I can jump from “debugging a Kubernetes pod” to “editing a code” in
seconds, all inside tmux.

# 📊 Popups for On-Demand Tools

The other secret weapon in my tmux config: fullscreen-like popups for CLI apps.

```tmux
bind-key h display-popup -E -w 80% -h 80% "htop"
bind-key g display-popup -E -w 80% -h 80% "gitui"
bind-key k display-popup -E -w 80% -h 80% "k9s"
```

All popups open at 80% width and height, so I see enough detail without losing
context in my main Tmux session. Close them with q or Ctrl+C, and you’re right
back where you left off.

## 💭 Final Thoughts

With a handful of smart plugins, a few quality-of-life keybindings, and the
magic of popups and Sesh, tmux becomes more than just a multiplexer - it’s a
personalized productivity cockpit.

The best part is that it grows with you. You can start small and keep layering
shortcuts, tools, and visual tweaks until tmux feels like an extension of your
brain.

If you haven’t tried Sesh for session management alongside popups for quick
tools, you might be surprised at how smooth and fluid your terminal workflow can
become. The less you think about managing your environment, the more you can
focus on actually building things.
