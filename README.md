# Ansible Role: omada_install

[![CI](https://github.com/trfore/ansible-role-omada-install/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/trfore/ansible-role-omada-install/actions/workflows/ci.yml)
[![CD](https://github.com/trfore/ansible-role-omada-install/actions/workflows/cd.yml/badge.svg)](https://github.com/trfore/ansible-role-omada-install/actions/workflows/cd.yml)

Install Omada SDN controller on RedHat/CentOS and Debian/Ubuntu.

This role installs the latest Omada SDN controller software using the latest tarball from https://www.tp-link.com/us/support/download/omada-software-controller/.

If you would like to manually download the tarball to your Ansible control host, download the `Omada_SDN_Controller_v5.*.*_Linux_x64.tar.gz`, to your `files` directory and set the following two variables in your playbook:

- `omada_tar_src: Omada_SDN_Controller_v5.*.*_Linux_x64.tar.gz`
- `omada_tar_src_remote: false`

See 'Example Playbooks' section for working examples. This role **does not configure the Omada controller**, it uses the default configuration values. It does set the service to run as a non-root user, you can change this by setting `omada_non_root: false`.

### Install the Role

You can install this role with the Ansible Galaxy CLI:

```bash
ansible-galaxy role install trfore.omada_install
```

## Tested Platforms and Versions

- MongoDB Community: `4.4.x`
- Omada SDN: `5.9.x`
- CentOS Stream 8
- Debian 10
- Ubuntu 20.04

## Requirements

- MongoDB Community Edition, `mongodb-org >=4.4.0`, a role for installing it via a package manager is available - `trfore.jsvc`.
- Apache Commons Daemon, `jsvc >= 1.1.0`, a role for installing the **latest** binary is available - `trfore.mongodb_install`.
- You can install these roles by creating a `requirements.yml` file and running `ansible-galaxy install -r requirements.yml`.

  ```yaml
  # requirements.yml
  ---
  roles:
    - name: trfore.jsvc
    - name: trfore.mongodb_install
    - name: trfore.omada_install
  ```

- NOTE: For **Ubuntu 20.04** targets, this role installs **OpenJDK 11**. While `jsvc` is available via APT, it is `< 1.1.0` and will **only work with OpenJDK 8**. If you prefer to use this older version, set `omada_dependencies` to the following in your playbook (see 'Example Playbooks' section below):

  ```yaml
  omada_dependencies: ['curl', 'openjdk-8-jre-headless', 'jsvc']
  ```

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable                | Default    | Description                                                                    | Required  |
| ----------------------- | ---------- | ------------------------------------------------------------------------------ | --------- |
| omada_tar_src           | URL        | Omada SDN tar file, URL or relative path                                       | No        |
| omada_tar_src_remote    | `true`     | Boolean, `true` if downloading from URL                                        | No        |
| omada_tar_dir           | `/var/tmp` | Temporary directory on the target host for extracting and installing Omada SDN | No        |
| omada_tar_folder        | Automatic  | Determined from the `omada_tar_src` variable                                   | Automatic |
| omada_non_root          | `true`     | Boolean, configure Omada SDN to run as a non-root user                         | No        |
| omada_remove_tar_folder | `false`    | Boolean, remove the temporary directory on the remote host                     | No        |

OS specific variables are listed below, along with default values (see `vars/debian.yml` and `vars/redhat.yml`):

| Variable           | Default                                       | Description                              | Required |
| ------------------ | --------------------------------------------- | ---------------------------------------- | -------- |
| omada_dependencies | `["curl", "openjdk-11-jre-headless"]`         | Required packages for Omada SDN (Debian) | No       |
| omada_dependencies | `["curl", "java-11-openjdk-headless.x86_64"]` | Required packages for Omada SDN (RHEL)   | No       |

## Dependencies

- Apache Commons Daemon, `jsvc >= 1.1.0`
- MongoDB Community Edition `mongodb-org >=4.4.0`

## Example Playbooks

```yaml
- hosts: servers
  become: true
  roles:
    - name: Install MongoDB Community
      role: trfore.mongodb_install

    - name: Install jsvc Binary
      role: trfore.jsvc

    - name: Install Omada SDN
      role: trfore.omada_install
```

- If you manually download the tar file.

```yaml
- hosts: servers
  become: true
  vars:
    omada_tar_src: Omada_SDN_Controller_v5.*.*_Linux_x64.tar.gz
    omada_tar_src_remote: false
  roles:
    - name: Install MongoDB Community
      role: trfore.mongodb_install

    - name: Install jsvc Binary
      role: trfore.jsvc

    - name: Install Omada SDN
      role: trfore.omada_install
```

- If you would like to install OpenJDK JRE 8 and jsvc using APT (Ubuntu 20.04 Only)

```yaml
- hosts: servers
  become: true
  vars:
    omada_dependencies: ['curl', 'openjdk-8-jre-headless', 'jsvc']
  roles:
    - name: Install MongoDB Community
      role: trfore.mongodb_install

    - name: Install Omada SDN
      role: trfore.omada_install
      when: ansible_distribution == 'Ubuntu'
```

## License

MIT

## Author Information

Taylor Fore (https://github.com/trfore)

## References

### Omada

- https://www.tp-link.com/us/support/download/omada-software-controller/
- https://www.tp-link.com/us/support/faq/3272/
- Run Omada SDN as non-root
  - https://www.tp-link.com/hk/support/faq/3583/
- Omada SDN Port List
  - https://www.tp-link.com/us/support/faq/865/
