# Runbook tiga VM

## Prasyarat

- Control node memiliki Ansible.
- Tiga VM Ubuntu 22.04 atau 24.04 dapat diakses melalui SSH.
- User SSH memiliki akses `sudo`.
- Public key control node sudah berada di `~/.ssh/authorized_keys` tiap VM.

Ubah IP dan `ansible_user` pada `inventory/hosts.yml`. Jangan commit password atau private key.

## Validasi koneksi

```bash
ansible-inventory --graph
ansible lab_servers -m ansible.builtin.ping
```

Tanpa VM, fan-out control node dapat diuji secara aman dengan modul `ping`:

```bash
ansible -i inventory/local-validation.yml lab_servers -m ansible.builtin.ping
```

Inventory tersebut memakai tiga alias untuk **satu** control node. Jangan gunakan inventory itu untuk menjalankan tiga playbook karena hasilnya bukan bukti eksekusi pada tiga VM dan dapat mengubah control node tiga kali.

Jika sudo meminta password, tambahkan `--ask-become-pass` pada perintah playbook. Jika private key tidak dimuat oleh SSH agent, gunakan `--private-key ~/.ssh/nama_key` dari command line.

## Eksekusi tiga playbook

```bash
ansible-playbook playbooks/01-user-management.yml --check --diff
ansible-playbook playbooks/01-user-management.yml

ansible-playbook playbooks/02-nginx-deployment.yml --check --diff
ansible-playbook playbooks/02-nginx-deployment.yml

ansible-playbook playbooks/03-security-hardening.yml --check --diff
ansible-playbook playbooks/03-security-hardening.yml \
  -e security_change_approved=true
```

Catatan: pada VM yang benar-benar baru, check mode untuk role package/service bisa memberi hasil parsial karena package yang disimulasikan belum benar-benar membuat user, directory, atau service. Ini batas check mode, bukan alasan untuk melewati staging; bootstrap package lebih dahulu pada lab atau validasi pada snapshot/canary.

Hardening memakai `serial: 1`. Untuk rollout awal yang lebih aman:

```bash
ansible-playbook playbooks/03-security-hardening.yml \
  --limit vm1 --check --diff

ansible-playbook playbooks/03-security-hardening.yml \
  --limit vm1 -e security_change_approved=true
```

Aktifkan SSH hardening hanya setelah public-key login dan console recovery diuji:

```bash
ansible-playbook playbooks/03-security-hardening.yml \
  --limit vm1 \
  -e security_change_approved=true \
  -e security_harden_ssh=true
```

## Membuktikan idempotensi

Jalankan playbook yang sama untuk kedua kalinya. Hasil ideal adalah `changed=0`. Perubahan legitimate, seperti update package metadata atau state dari luar Ansible, perlu dijelaskan.

## Verifikasi hasil

```bash
ansible lab_servers -b -m ansible.builtin.command -a 'id ansible_lab'
ansible lab_servers -m ansible.builtin.uri -a 'url=http://localhost return_content=true'
ansible lab_servers -b -m ansible.builtin.command -a 'auditctl -s'
```

## Rollback

- User: kelola state user melalui variable; jangan menghapus administrator terakhir.
- Nginx: kembalikan symlink site sebelumnya dan validasi dengan `nginx -t`.
- Security: rollback per kontrol, bukan membalik semua perubahan secara buta.
- SSH/kernel: siapkan snapshot VM dan console access sebelum perubahan.
