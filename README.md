
# Ansible Role: Zeek ([Ludus](https://ludus.cloud))

An Ansible role to deploy [Zeek](https://github.com/zeek/zeek) on Debian and Ubuntu hosts in Ludus environments.

> [!WARNING]
> This role deploys a minimal, unoptimized Zeek setup using the VM’s primary network interface for packet capture. Intended only for small, controlled test environments. Capturing high volumes of traffic may lead to packet loss, performance degradation, or unstable behavior.

> [!INFO]
> This role is for general Zeek deployment in Ludus. If you want Zeek for a Splunk Attack Range, use [@P4T12ICK's role](https://github.com/P4T12ICK/ludus_ar_zeek) instead.

## Requirements

- Supported: Debian bookworm/12, trixie/13; Ubuntu jammy/22.04, noble/24.04, questing/25.10, resolute/26.04.
- Zeek is installed from the openSUSE Build Service (OBS) repo, with URLs built dynamically for supported versions.
- Tested on Ubuntu 22.04 (jammy) and Debian 12 (bookworm).
- For VLAN-wide capture, disable `bridge-ageing` on the Proxmox host’s bridge (see [Ludus docs](https://docs.ludus.cloud/docs/networking#packet-capture)).

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