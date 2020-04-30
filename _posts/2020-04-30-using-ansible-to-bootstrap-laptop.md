---
layout: post
title: Using Ansible to Bootstrap macOS But Note That We're Not AnotherBlogTeachingYouHowToAnsible
---

After a great deal of deliberation, I had decided to replace my laptop. The laptop was an 4 year old MacBook Air that worked well outside of an ever-shortening battery life. The replacement is YetAnotherMacBookAir, and likely the last model Apple will issue that doesn't come with a Touch Bar. Touch Bars are my nemesis. I hate them. But I digress. 

I decided that it was time to write an Ansible playbook to cover some of the things that would need to be done for any laptop that I need to set up.

This post will not teach you about how to use Ansible. It will just break down the decisions made behind the project, which is located here: [bootstrap](https://github.com/laurendc/bootstrap)

Other things that I won't do in this post:
* Go into why I chose to implement one task or another.
* Do a walkthrough of every module that's used here
* Discuss breaking these down into roles. Yeah, I know we can do these things. We're not doing them now. 

# What We're Managing
* dotfile setup and configuration
* New ssh key generation & adding to Github
* Installation of packages via Homebrew

Here is the directory structure of the project:

```
[~/repos/bootstrap] $ tree
.
├── README.md
├── defaults
│   ├── dotfiles.yml
│   ├── main.yml
│   └── packages.yml
├── play_dev_setup.yml
├── tasks
│   ├── dotfiles.yml
│   └── sshkeys.yml
└── templates
    └── ssh_config.j2

3 directories, 8 files
```
This directory structure follows the concept of breaking up various groups of variables and tasks into seprarate `.yml` files. For smaller projects, this is preferable for me as it keeps it nicely readable.

## What we're doing:

### Delegating package installations
  
Packages installations are handled with [Homebrew](https://brew.sh/), which has to be installed first.  We have our desired packages split into two lists based on which type of Homebrew package they are and these are located in the `defaults/packages.yml`:

```
brew_packages:
  - git
  - vim
  - go
  - fortune
  - cowsay
  - lolcat
  - tree
brew_cask_packages:
  - visual-studio-code
  - iterm2
  - google-chrome
  - spotify
  - docker
  - slack
  - keepassx
  - onedrive
  - virtualbox
  - signal
  - istat-menus
```

We manage our `brew` and `brew cask` installation tasks separately in order to take advantage of disparate tagging, which is nice for maintaining the play over the course of time. Here they are in the `main.yml`:

{% raw %}
```yaml
    - name: brew install packages
      homebrew: 
        name:
          "{{ brew_packages }}"
        state: present
      tags:
        - install
        - install_brew_packages

    - name: brew cask install packages
      homebrew_cask:
        name:
          "{{ brew_cask_packages }}"
        state: present
      tags:
        - install
        - install_brew_cask_packages
```
{% endraw %}
### Setting up an ssh key and pushing it up to Github
To do this, we need a Github Access key. I'm using `ansible-vault` to manage this. From there, we have the following tasks which work flawlessly to generate our keypair and push the public key up to our Github:

```yaml
- name: Create directory for ssh keys
  file:
    path: "{{ profile_dir }}/.ssh"
    state: directory
    mode: '0700'

- name: Generate ssh keypair
  openssh_keypair:
    path: "{{ profile_dir }}/.ssh/{{ github_ssh_key }}"
  register: ssh_key
    
- name: Add key to Github
  github_key:
    name: GitHub Access key for "{{ ansible_hostname }}"
    token: "{{ github_token }}"
    pubkey: "{{ ssh_public_key }}"
  when: ssh_key is succeeded

- name: Add new Github key to .ssh config
  template:
    src: templates/ssh_config.j2
    dest: "{{ profile_dir }}/.ssh/config"
    owner: "{{ username }}"
    group: staff
    mode: '0600'
    backup: yes
```

### Configuring already existing dotfiles
We're doing a bit of cheating here. We're cloning an existing dotfiles repository from Github, and then running a shell script located within the project that will create symlinks to the dotfiles listed within the project. This also contains a <strike>horrible-but-functional</strike> bash script that I'll have to port over to <strike>Go</strike> Ansible eventually. 

```yaml
- name: Clone dotfiles
  git:
    repo: git@github.com:laurendc/dotfiles.git
    dest: "{{ repo_home }}/dotfiles/"
    key_file: "{{ profile_dir }}/.ssh/{{ github_ssh_key }}"

# This bash script really needs to get ported over to Ansible.
# TODO: Port this over to Ansible.
- name: Run shell script in dotfile repo to set up dotfiles
  shell: "{{ repo_home }}/dotfiles/makesymlinks.sh"
  ignore_errors: True
```

For my current needs, this is a playbook that is more than suitable.

Wishlist items:
* Break package management into a reusable role
* Add print driver management
* Port bash script over to <strike>Go</strike> Ansible