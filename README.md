# SOLACE-CICD

A GitOps pipeline that provisions Solace PubSub+ broker configuration (Message VPNs, Queues, Client Usernames, Topic Subscriptions) using **Ansible** and **Jenkins**, driven entirely by JSON config files committed to this repo.

## How it works

1. You drop a JSON file into `configs/`, named with a `CREATE_` or `MODIFY_` prefix.
2. You push (or commit directly on GitHub) to `main`.
3. Jenkins checks out the repo and runs Ansible against the target broker.
4. `deploy_solace.yml` scans `configs/*.json`, and for each file loops into `inner_provision.yml`, which reads the filename prefix to decide whether to `POST` (create) or `PATCH` (modify) via the Solace SEMP v2 REST API.

```
SOLACE-CICD/
├── configs/                  # JSON payloads — filename prefix (CREATE_/MODIFY_) drives behavior
│   └── MODIFY_advanced_queue.json
├── inventory/
│   ├── dev.ini                # localhost:8080
│   └── prod.ini                # localhost:8081
├── playbooks/
│   ├── deploy_solace.yml       # entry point — discovers config files
│   └── inner_provision.yml     # per-file task logic (CREATE/MODIFY → SEMP call)
├── Jenkinsfile
└── README.md
```

## Config file schema

Each file in `configs/` describes one SEMP object change:

```json
{
  "vpn_name": "FinanceCoreVPN",
  "queue_name": "orders.processing.v1.q",
  "access_type": "exclusive",
  "max_spool_usage_mb": 5000,
  "max_msg_size_bytes": 10485760,
  "respect_ttl": true,
  "dead_letter_queue": "orders.poison.v1.q",
  "topic_subscriptions": [
    "finance/orders/v1/+"
  ]
}
```

- **`CREATE_*.json`** → `POST`s a new queue (and binds any listed topic subscriptions).
- **`MODIFY_*.json`** → `PATCH`es an existing queue's parameters.

## Jenkins setup

1. **Manage Jenkins → Credentials** → add a `Username with password` credential with ID exactly `solace_admin_creds` (your Solace SEMP admin user/pass).
2. **New Item → Pipeline** → Pipeline script from SCM → Git → this repo URL → Script Path: `Jenkinsfile`.
3. Click **Build Now**. The pipeline runs against Dev (`inventory/dev.ini`, port 8080) then Prod (`inventory/prod.ini`, port 8081).

## Local broker ports

| Environment | Port | Inventory file |
|---|---|---|
| Dev  | 8080 | `inventory/dev.ini`  |
| Prod | 8081 | `inventory/prod.ini` |

## Notes

- Playbooks run with `connection: local` — Ansible calls the SEMP REST API directly rather than SSHing into a target host.
- No credentials are hardcoded anywhere in this repo; `semp_user`/`semp_pass` are injected at runtime from the Jenkins credential store.
