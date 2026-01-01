# Ansible Role: Zeek ([Ludus](https://ludus.cloud))

An Ansible role to deploy [Zeek](https://github.com/zeek/zeek) on Debian and Ubuntu hosts in Ludus environments, providing network telemetry until native Zeek support lands in the Ludus flavor of the [Splunk Attack Range](https://github.com/splunk/attack_range).

> [!WARNING]
> This role deploys a very minimal and unoptimized Zeek setup that uses the VM’s primary network interface for packet capture. This configuration probably violates multiple best practices and is intended only for small, controlled test environments. Capturing high volumes of traffic may lead to packet loss, performance degradation, or unstable behavior.

## Requirements

- A Debian (bullseye, bookworm, trixie) or Ubuntu (focal, jammy, noble) host.  
- If you wish to send the Zeek logs to Splunk, your life will be made easier if you also have the `p4t12ick.ludus_ar_linux` role on the host you are deploying Zeek to. This is not mandatory however, just set the `ludus_zeek_splunk_forwarder_enabled` variable to `false` if you are not doing this.
- To capture traffic across the entire VLAN, `bridge-ageing` must be disabled on the bridge interface of the range **on the Proxmox host**. Make sure to check [the Ludus documentation](https://docs.ludus.cloud/docs/networking#packet-capture).

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Optionally install Zeek addons via zkg - was meant for BZAR but it doesn't seem to work very well with recent Zeek versions
ludus_zeek_install_addons: false
ludus_zeek_addons:
  - bzar

# If used with Splunk Attack Range, Zeek logs can be forwarded to Splunk using the existing Splunk Universal Forwarder
# When set to true, the role copies the `inputs.conf` file to the host and restarts the UF service
# You only need to install the Zeek TA on the Splunk server to consume the logs
ludus_zeek_splunk_forwarder_enabled: true

# Zeek OBS repository base
ludus_zeek_repo_base: "https://download.opensuse.org/repositories/security:/zeek"

# NIC configuration
# `ens18` is the default interface on Ludus VMs (at least in my environment)
# Promiscuous mode must be enabled to capture all traffic on the interface
ludus_zeek_nic_name: "ens18"
ludus_zeek_nic_promisc: true
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
  - vm_name: "{{ range_id }}-ar-linux"
    hostname: "ar-linux"
    template: ubuntu-22.04-x64-server-template
    vlan: 20
    ip_last_octet: 11
    ram_gb: 4
    cpus: 2
    linux: true
    roles:
      - p4t12ick.ludus_ar_linux
      - 5tuk0v.ludus_zeek
    role_vars:
      ludus_ar_linux_splunk_ip: "10.5.20.10"
```

## License

MIT

## Author Information

This role was created by [5tuk0v](https://github.com/5tuk0v), for [Ludus](https://ludus.cloud/).

- GitHub: [@5tuk0v](https://github.com/5tuk0v)
- Twitter/X: [@stuk0v_](https://twitter.com/stuk0v_)