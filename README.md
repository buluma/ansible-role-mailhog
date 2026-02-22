# [Ansible role mailhog](#ansible-role-mailhog)

MailHog for Linux

|GitHub|GitLab|Downloads|Version|
|------|------|---------|-------|
|[![github](https://github.com/buluma/ansible-role-mailhog/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-mailhog/actions)|[![gitlab](https://gitlab.com/shadowwalker/ansible-role-mailhog/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-mailhog)|[![downloads](https://img.shields.io/ansible/role/d/buluma/mailhog)](https://galaxy.ansible.com/buluma/mailhog)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-mailhog.svg)](https://github.com/buluma/ansible-role-mailhog/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-mailhog/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
- become: false
  hosts: all
  name: Converge
  post_tasks:
  - ansible.builtin.copy:
      dest: /tmp/test-message
      mode: 420
      src: test-message
    name: Copy test message into place.
  - changed_when: false
    name: Send an email via mhsendmail.
    shell: cat /tmp/test-message | /opt/mailhog/mhsendmail johndoe@example.com
  - ansible.builtin.uri:
      url: http://localhost:8025/api/v2/messages
    delay: 1
    name: Test retrieiving messages from the MailHog API.
    register: result
    retries: 60
    until: result.status == 200
  pre_tasks:
  - apt: update_cache=true cache_valid_time=600
    name: Update apt cache.
    when: ansible_os_family == 'Debian'
  - ansible.builtin.package:
      name:
      - '@Development tools'
      - tar
      - unzip
      - net-tools
      - curl
      state: present
    name: Ensure build dependencies are installed (RedHat).
    when: ansible_os_family == 'RedHat'
  - ansible.builtin.apt:
      name:
      - build-essential
      - tar
      - unzip
      - net-tools
      - curl
      state: present
    name: Ensure build dependencies are installed (Debian).
    when: ansible_os_family == 'Debian'
  roles:
  - buluma.daemonize
  - ansible-role-mailhog
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-mailhog/blob/master/molecule/default/prepare.yml):

```yaml
- become: true
  gather_facts: false
  hosts: all
  name: Prepare
  roles:
  - role: buluma.bootstrap
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/buluma/ansible-role-mailhog/blob/master/defaults/main.yml):

```yaml
mailhog_binary_url: https://github.com/mailhog/MailHog/releases/download/v{{ mailhog_version
  }}/MailHog_linux_amd64
mailhog_daemonize_bin_path: /usr/sbin/daemonize
mailhog_install_dir: /opt/mailhog
mailhog_version: 1.0.0
mhsendmail_binary_url: https://github.com/mailhog/mhsendmail/releases/download/v{{
  mhsendmail_version }}/mhsendmail_linux_amd64
mhsendmail_version: 0.2.0
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-mailhog/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Build Status GitHub](https://github.com/buluma/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-bootstrap/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-bootstrap)|
|[buluma.daemonize](https://galaxy.ansible.com/buluma/daemonize)|[![Build Status GitHub](https://github.com/buluma/ansible-role-daemonize/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-daemonize/actions)|[![Build Status GitLab](https://gitlab.com/shadowwalker/ansible-role-daemonize/badges/master/pipeline.svg)](https://gitlab.com/shadowwalker/ansible-role-daemonize)|

## [Dependencies](#dependencies)

Most roles require some kind of preparation, this is done in `molecule/default/prepare.yml`. This role has a "hard" dependency on the following roles:

- {'role': 'buluma.daemonize'}

## [Context](#context)

This role is part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-mailhog/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/r/buluma/enterpriselinux)|all|
|[Ubuntu](https://hub.docker.com/r/buluma/ubuntu)|all|
|[Debian](https://hub.docker.com/r/buluma/debian)|all|

The minimum version of Ansible required is 2.4, tests have been done on:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them on [GitHub](https://github.com/buluma/ansible-role-mailhog/issues).

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-mailhog/blob/master/LICENSE).

## [Author Information](#author-information)

[buluma](https://buluma.github.io/)

