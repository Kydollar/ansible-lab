# Ansible Interview Lab

Lab pemula untuk mendemonstrasikan tiga use case Ansible pada tiga VM Ubuntu.

| Playbook | Use case | Konsep yang didemokan |
|---|---|---|
| `01-user-management.yml` | Standardisasi administrator | variables, loop, idempotensi, sudo validation |
| `02-nginx-deployment.yml` | Deployment web server | package, template Jinja2, handler, rolling update |
| `03-security-hardening.yml` | Baseline hardening terinspirasi CIS | assertions, tags, opt-in control, safe rollout |

Semua playbook menargetkan `lab_servers`, yaitu `vm1`, `vm2`, dan `vm3` pada `inventory/hosts.yml`.

## Mulai cepat

```bash
ansible-inventory --graph
ansible lab_servers -m ansible.builtin.ping
ansible-playbook playbooks/01-user-management.yml --check --diff
```

Lanjutkan dengan [runbook eksekusi](docs/runbook.md), [ringkasan CIS Benchmark](docs/cis-benchmark.md), dan [catatan interview](docs/interview-notes.md).

## Struktur

```text
inventory/              inventory tiga VM dan group variables
playbooks/              tiga entry-point use case
roles/user/             manajemen akun
roles/nginx/            deployment Nginx
roles/security/         baseline security edukasional
docs/                   materi CIS dan runbook
site.yml                menjalankan ketiga playbook
```

## Catatan keamanan

- Inventory tidak berisi secret; gunakan SSH agent, Ansible Vault, atau secret manager.
- Jalankan `--syntax-check`, lalu `--check --diff`, canary satu VM, dan baru rollout.
- `security_harden_ssh` default `false` untuk mencegah lockout.
- Role security bukan implementasi atau sertifikasi CIS lengkap.

Referensi belajar utama: [Ansible Playbook Guide](https://docs.ansible.com/projects/ansible/latest/playbook_guide/), [Ansible Inventory Guide](https://docs.ansible.com/projects/ansible/latest/inventory_guide/intro_inventory.html), dan [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks-overview).
