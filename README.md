<p align="center"><img src="https://cdn.jsdelivr.net/gh/keindev/home-services/media/banner.svg" alt="banner"></p>

---

<p align="center" style="display: flex; justify-content: center; gap: 10px;">
<img width="60" src="https://cdn.jsdelivr.net/gh/keindev/home-services/media/icons/nas.png" alt="NAS">
<img width="60" src="https://cdn.jsdelivr.net/gh/keindev/home-services/media/icons/bots/alert-manager.png" alt="Alert manager">
<img width="60" src="https://cdn.jsdelivr.net/gh/keindev/home-services/media/icons/bots/backup.png" alt="Backup">
<img width="60" src="https://cdn.jsdelivr.net/gh/keindev/home-services/media/icons/bots/uptime-kuma.png" alt="Uptime Kuma">
<img width="60" src="https://cdn.jsdelivr.net/gh/keindev/home-services/media/icons/bots/watchtower.png" alt="Watchtower">
</p>

In my case, I am using:

- `"Iron Man" Franky` (_franky 10.10.10.1_) - for services UI's, proxy and DNS
- `"Cotton Candy Lover" Chopper` (_chopper 192.168.107_) - for DB, Redis and other storage types
- `"Knight of the Sea" Jinbe` (_jimbe 192.168.108_) - for monitoring
- `user` - as user in nodes `franky` & `chopper`
- `user@example.com` - as main email address
- `example.com` - as main domain

---

- Services:
  - [Portainer](https://docs.portainer.io/start/install-ce)
  - [Penpot](https://penpot.app/self-host)
  - [Pi-hole](https://pi-hole.net/)
  - [Planka](https://planka.app/)
  - [Gitea](https://about.gitea.com/)
  - [Traefik](https://traefik.io/)
  - [pgAdmin](https://www.pgadmin.org/)
- Monitoring:
  - [Grafana](https://grafana.com/)
  - [Prometheus](https://prometheus.io/)
  - [cAdvisor](https://github.com/google/cadvisor)
  - [Node exporter](https://github.com/prometheus/node_exporter)
  - [Uptime Kuma](https://github.com/louislam/uptime-kuma)
  - [Alert manager](https://prometheus.io/docs/alerting/latest/alertmanager/)
  - [Watchtower](https://github.com/containrrr/watchtower)
  - [bots](https://telegram.org/)
- Storage:
  - [PostgreSQL](https://www.postgresql.org/)
  - [MinIO](https://min.io/)
  - [Redis](https://redis.io/)
- Backup:
  - [Docker volume backup](https://github.com/offen/docker-volume-backup)

## Generating a new SSH key and adding it to the ssh-agent

Paste the text below, substituting email address. (_in `~/.ssh` directory_)

```
$ ssh-keygen -t ed25519 -C "user@example.com"
```

Copy public key to nodes.

```
$ ssh-copy-id -i ./services.pub user@10.10.10.1
$ ssh-copy-id -i ./services.pub user@10.10.10.2
$ ssh-copy-id -i ./services.pub user@10.10.10.3
```

Check SSH access.

```
$ ssh user@10.10.10.x
```

## Change nodes and variables in:

- [inventory.ini](./inventory.ini) - hosts
- [ansible.cfg](./ansible.cfg) - config
- [vars/base.yml](./vars/base.yml) - variables
- [vars/secrets.yml](./vars/secrets.yml) - secrets

Rename `secrets.example.yml` to `secrets.yml`, add your secrets and run:

```
$ ansible-vault encrypt ./vars/secrets.yml
```

### Show inventory list and `groups`/`subgroups` naming:

```console
$ ansible-inventory --list
```

<details>
<summary>Output:</summary>

```json
{
  "_meta": {
    "hostvars": {
      "chopper": { ... },
      "franky": { ... },
      "jinbe": { ... }
    }
  },
  "all": {
    "children": ["monitoring", "servers", "services", "storages", "ungrouped"]
  },
  "monitoring": {
    "hosts": ["jinbe"]
  },
  "servers": {
    "hosts": ["chopper", "franky", "jinbe"]
  },
  "services": {
    "hosts": ["franky"]
  },
  "storages": {
    "hosts": ["chopper"]
  }
}
```

</details>

### Check when inventory nodes exists and available:

```console
$ ansible all -m ping
```

<details>
<summary>Output:</summary>

```json

franky | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

chopper | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

jinbe | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}


```

</details>

## Run

```console
$ ansible-playbook run.yml
```

## Post config

#### Planka default login

The default logins are defined as:

- Username: `demo@demo.demo`
- Password: `demo`

#### Penpot user create

```console
docker exec -ti penpot-backend python3 ./manage.py create-profile
```
