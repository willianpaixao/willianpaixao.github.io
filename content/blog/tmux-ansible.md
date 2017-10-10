---
title: "Tmux and Ansible"
date: 2017-10-10T14:57:31+02:00
tags: ["ansible", "tmux", "devops", "linux", "shell"]
categories: ["ansible", "devops"]
draft: false
---
![Terminal running tmux](https://i.imgur.com/p7ApcO6.png)

[Tmux](https://github.com/tmux/tmux) is a "terminal multiplexer" that allows you
to have multiples window terminals in a single terminal. And our beloved
[Ansible](https://github.com/ansible/ansible) can help install and setup `tmux`.

## Installing
You need first install Tmux's package. Among the several ways, we'll just use
the standard and offical package. This works for most of the operating systems.

```
- name: Install tmux
  package:
    name: tmux
    state: present
  tags: tmux
```

With just that you have a working `tmux`. But let's make it fancier.

## Tmux Package Manager - TPM

1. [TPM](https://github.com/tmux-plugins/tpm) is an plugin manager for `tmux`
   and it's pretty simple to install. You basically just have to clone the Git
repo and call it in your tmux config file.

```
- name: Clone TPM repository
  git:
    repo: 'https://github.com/tmux-plugins/tpm.git'
    dest: /home/willian/.tmux/plugins/tpm
  become: true
  become_user: willian
  tags: tmux
```

2. Then create a file called `.tmux.conf` in your home folder with the following
   content:

```
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-copycat'
set -g @plugin 'tmux-plugins/tmux-pain-control'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-yank'
set -g @plugin 'seebi/tmux-colors-solarized'
set -g @colors-solarized 'dark'

run '~/.tmux/plugins/tpm/tpm'
```

3. Lastly, press `prefix` + `I` (Ctrl + B + I by default) to install all the
   plugins.
4. If you want `tmux` to start by default with every terminal you open, add this
   to your `.bashrc`.

```
if [[ -z "$TMUX" ]]; then
    ID=$(/usr/bin/tmux ls | grep -vm1 attached | cut -d: -f1)
    if [[ -z "${ID}" ]]; then
        /usr/bin/tmux new-session
    else
        /usr/bin/tmux attach-session -t "${ID}"
    fi
fi
```

Steps **2.** and **4.** can be automated with Ansible too. See Ansible's [file
module](http://docs.ansible.com/ansible/latest/file_module.html) for further
information.

Done. :)
Feedback is always welcome, so feel free to comment any tweaks you do in your
system.

## References

1. These code snippets were extracted from my Ansible
   [playbooks](https://github.com/willianpaixao/playbooks) and config file's
[repository](https://github.com/willianpaixao/dotfiles).
