# Ansible role [mailhog](https://galaxy.ansible.com/ui/standalone/roles/buluma/mailhog/documentation)

MailHog for Linux

|GitHub|Version|Issues|Pull Requests|Downloads|
|------|-------|------|-------------|---------|
|[![github](https://github.com/buluma/ansible-role-mailhog/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-mailhog/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-mailhog.svg)](https://github.com/buluma/ansible-role-mailhog/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-mailhog.svg)](https://github.com/buluma/ansible-role-mailhog/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-mailhog.svg)](https://github.com/buluma/ansible-role-mailhog/pulls/)|[![Ansible Role](https://img.shields.io/ansible/role/d/buluma/mailhog)](https://galaxy.ansible.com/ui/standalone/roles/buluma/mailhog/documentation)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-mailhog/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: false

  pre_tasks:
    - name: Update apt cache.
      apt: update_cache=true cache_valid_time=600
      when: ansible_os_family == 'Debian'

    - name: Ensure build dependencies are installed (RedHat).
      ansible.builtin.package:
        name:
          - "@Development tools"
          - tar
          - unzip
          - net-tools
          - curl
        state: present
      when: ansible_os_family == 'RedHat'

    - name: Ensure build dependencies are installed (Debian).
      ansible.builtin.apt:
        name:
          - build-essential
          - tar
          - unzip
          - net-tools
          - curl
        state: present
      when: ansible_os_family == 'Debian'

  roles:
    - buluma.daemonize
    - buluma.mailhog

  post_tasks:
    - name: Copy test message into place.
      ansible.builtin.copy:
        src: test-message
        dest: /tmp/test-message
        mode: 0644

    - name: Send an email via mhsendmail.
      shell: cat /tmp/test-message | /opt/mailhog/mhsendmail johndoe@example.com
      changed_when: false

    - name: Test retrieiving messages from the MailHog API.
      ansible.builtin.uri:
        url: http://localhost:8025/api/v2/messages
      register: result
      until: result.status == 200
      retries: 60
      delay: 1
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-mailhog/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: false

  roles:
    - role: buluma.bootstrap
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-mailhog/blob/master/defaults/main.yml):

```yaml
---
mailhog_install_dir: /opt/mailhog
mailhog_version: "1.0.0"
mailhog_binary_url: "https://github.com/mailhog/MailHog/releases/download/v{{ mailhog_version }}/MailHog_linux_amd64"
mhsendmail_version: "0.2.0"
mhsendmail_binary_url: "https://github.com/mailhog/mhsendmail/releases/download/v{{ mhsendmail_version }}/mhsendmail_linux_amd64"

# Path to daemonize, which is used to launch MailHog via init script.
mailhog_daemonize_bin_path: /usr/sbin/daemonize
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-mailhog/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | Version |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Ansible Molecule](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bootstrap.svg)](https://github.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.daemonize](https://galaxy.ansible.com/buluma/daemonize)|[![Ansible Molecule](https://github.com/buluma/ansible-role-daemonize/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-daemonize/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-daemonize.svg)](https://github.com/shadowwalker/ansible-role-daemonize)|

## [Dependencies](#dependencies)

Most roles require some kind of preparation, this is done in `molecule/default/prepare.yml`. This role has a "hard" dependency on the following roles:

- {'role': 'buluma.daemonize'}

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-mailhog/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/r/buluma/enterpriselinux)|8, 9|
|[Ubuntu](https://hub.docker.com/r/buluma/ubuntu)|all|
|[Debian](https://hub.docker.com/r/buluma/debian)|all|

The minimum version of Ansible required is 2.4, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-mailhog/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-mailhog/blob/master/CHANGELOG.md)

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-mailhog/blob/master/LICENSE)

## [Author Information](#author-information)

[Shadow Walker](https://buluma.github.io/)
