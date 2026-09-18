# Node Exporter Ansible Role

This Ansible role is designed to automate the deployment and configuration of [Prometheus Node Exporter](https://github.com/prometheus/node_exporter).

The role installs Node Exporter from a binary release, configures it as a systemd service, and allows managing the service lifecycle through Ansible.

## Using the role

Include the role in your Ansible playbook and apply it to the hosts where Node Exporter should be installed.

## Example playbook

```yaml
- hosts: node_exporter_hosts
  become: true

  roles:
    - role: node_exporter
```

The role requires elevated privileges to install the Node Exporter binary, create the systemd unit, and configure the required directories and files.

If the Ansible user does not have `NOPASSWD` sudo access and is not the `root` user, you need to provide the become password when running the playbook.

For example:

```bash
ansible-playbook -i inventory.yml playbook.yml --ask-become-pass
```

## What the role does

The role automates the following tasks:

* Downloads the Node Exporter binary from the specified release.
* Installs Node Exporter on the target host.
* Creates the required system user.
* Creates the required directories.
* Configures Node Exporter as a systemd service.
* Configures the Node Exporter listening port.
* Enables Node Exporter to start automatically after system boot.
* Starts and restarts the Node Exporter service when required.
* Verifies that Node Exporter is running and available.

Node Exporter exposes host-level hardware and operating system metrics through an HTTP endpoint that can be scraped by Prometheus.

By default, the metrics endpoint is available at:

```text
http://<host>:<port>/metrics
```

## Node Exporter service

After installation, Node Exporter is managed through systemd.

The service can be checked with:

```bash
systemctl status node_exporter
```

Started manually with:

```bash
systemctl start node_exporter
```

Restarted with:

```bash
systemctl restart node_exporter
```

And enabled at boot with:

```bash
systemctl enable node_exporter
```

The exported metrics can be checked with:

```bash
curl http://localhost:<port>/metrics
```

## Prometheus integration

After Node Exporter has been installed, Prometheus can scrape its `/metrics` endpoint.

Example Prometheus configuration:

```yaml
scrape_configs:
  - job_name: node_exporter
    static_configs:
      - targets:
          - server01:9100
          - server02:9100
```

The exact port depends on the configuration used for the role.

## Requirements

* Ansible
* Linux target host
* systemd
* x86_64 or another supported architecture
* Internet access from the target host if the Node Exporter binary is downloaded directly
* Privileged access (`root` or `sudo`)

## Role structure

The role follows the standard Ansible role structure:

```text
node_exporter/
├── defaults/
│   └── main.yml
├── handlers/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
│   └── node_exporter.service.j2
├── files/
├── meta/
│   └── main.yml
└── README.md
```

## Contributing

Contributions are highly welcome.

Ways to help:

* Bug reports and feature requests
* Pull requests with improvements
* Additional tests
