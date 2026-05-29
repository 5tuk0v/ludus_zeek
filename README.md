# Ansible Role: Zeek ([Ludus](https://ludus.cloud))

An Ansible role to deploy [Zeek](https://github.com/zeek/zeek) on Debian and Ubuntu hosts in Ludus environments. 

**I initially made this role to add Zeek to my Ludus [Splunk Attack Range](https://github.com/splunk/attack_range). Shortly after publishing it, I realized that @P4T12ICK had already made an [Attack Range-specific role for Ludus](https://github.com/P4T12ICK/ludus_ar_zeek), and I didn't know about it. Lesson learned, next time I need to check Galaxy beforehand. If you are looking to add Zeek to your Ludus Splunk Attack Range, definitely use P4T12ICK's role. I have since removed all Splunk parts from this role, but figured I could keep it around for people who want to simply add Zeek to a range without Splunk.**

> [!WARNING]
> This role deploys a very minimal and unoptimized Zeek setup that uses the VM’s primary network interface for packet capture. This configuration probably violates multiple best practices and is intended only for small, controlled test environments. Capturing high volumes of traffic may lead to packet loss, performance degradation, or unstable behavior.

- Supported: Debian bookworm/12, trixie/13; Ubuntu jammy/22.04, noble/24.04, questing/25.10, resolute/26.04.
- Zeek is installed from the openSUSE Build Service (OBS) repo, with URLs built dynamically for supported versions.
- Tested on Ubuntu 22.04 (jammy) and Debian 12 (bookworm).
- For VLAN-wide capture, disable `bridge-ageing` on the Proxmox host’s bridge (see Ludus docs).

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Optionally install Zeek addons via zkg - was meant for BZAR but it doesn't seem to work very well with recent Zeek versions
ludus_zeek_install_addons: false
ludus_zeek_addons:
  - bzar

# Zeek OBS repository base
ludus_zeek_repo_base: "https://download.opensuse.org/repositories/security:/zeek"

# NIC configuration
# ens18 is the default interface on Ludus VMs (at least in my environment)
ludus_zeek_nic_name: "ens18"
```

## Dependencies

None.

## Example Playbook

```yaml
- hosts: zeek_hosts
  roles:
    - 5tuk0v.ludus_zeek
  vars:
    ludus_zeek_nic_name: "eth4"
```

## Example Ludus Range Config

```yaml
ludus:
  - vm_name: "{{ range_id }}-ubuntu-zeek"
    hostname: "linux-zeek"
    template: ubuntu-22.04-x64-server-template
    vlan: 20
    ip_last_octet: 11
    ram_gb: 4
    cpus: 2
    linux: true
    roles:
      - 5tuk0v.ludus_zeek
```

## License

MIT

## Author Information

This role was created by [5tuk0v](https://github.com/5tuk0v), for [Ludus](https://ludus.cloud/).

- GitHub: [@5tuk0v](https://github.com/5tuk0v)
- Twitter/X: [@stuk0v_](https://twitter.com/stuk0v_)