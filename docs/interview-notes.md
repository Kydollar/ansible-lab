# Catatan belajar dan interview Ansible

## Konsep inti

- **Control node:** mesin tempat Ansible dijalankan.
- **Managed node:** server/VM yang dikonfigurasi, umumnya melalui SSH.
- **Inventory:** daftar host, group, dan variable target.
- **Module:** unit kerja seperti `apt`, `user`, `template`, atau `service`.
- **Task:** pemanggilan sebuah module dengan desired state.
- **Play:** mapping antara host dan sekumpulan task/role.
- **Playbook:** satu atau lebih play dalam YAML.
- **Role:** struktur reusable berisi tasks, handlers, templates, defaults, dan metadata.
- **Handler:** task yang hanya berjalan ketika diberi `notify` oleh perubahan.
- **Facts:** informasi target yang dikumpulkan Ansible.
- **Idempotent:** eksekusi berulang mempertahankan state yang sama tanpa perubahan tidak perlu.

## Tiga use case yang bisa diceritakan

### 1. User lifecycle

Masalah: akun administrator berbeda-beda pada tiap server. Ansible membuat user, group, home, public key, dan optional sudo policy secara konsisten. Nilai interview: loop, variables, secret hygiene, dan idempotensi.

### 2. Application/web deployment

Masalah: konfigurasi web manual rawan drift. Ansible menginstal package, merender template host-specific, memvalidasi Nginx, lalu reload hanya jika konfigurasi berubah. Nilai interview: templates, handlers, validation, dan rolling update.

### 3. Security hardening

Masalah: baseline keamanan harus repeatable dan auditable. Ansible menerapkan policy terpilih, tetapi rollout dilakukan dengan check mode, canary, `serial`, opt-in untuk perubahan berisiko, dan exception management. Nilai interview: compliance bukan sekadar menjalankan playbook.

## Pertanyaan interview umum

**Apa beda ad-hoc command dan playbook?**  
Ad-hoc command cocok untuk tindakan satu kali. Playbook disimpan di Git, dapat direview, diulang, diuji, dan membentuk dokumentasi executable.

**Mengapa idempotensi penting?**  
Karena automation harus aman dijalankan berulang; hanya drift yang dikoreksi. Modul deklaratif lebih disukai daripada `shell` atau `command` tanpa guard.

**Bagaimana menangani secret?**  
Jangan commit plaintext. Gunakan Ansible Vault atau secret manager, batasi akses, dan gunakan `no_log` hanya untuk mencegah output—bukan sebagai enkripsi.

**Bagaimana rollout hardening tanpa lockout?**  
Backup/snapshot dan console access, syntax check, check/diff, canary satu host, validasi koneksi baru, `serial`, health check, lalu rollout. Firewall membuka SSH sebelum default deny; SSH config divalidasi sebelum reload.

**Apakah check mode menjamin perubahan aman?**  
Tidak. Check mode adalah simulasi dan dukungannya bergantung module; conditional atau dependency yang belum ada dapat membuat hasil parsial. Tetap perlu test environment dan canary.

**Bagaimana membuktikan compliance?**  
Sebutkan benchmark/profile/version/scope, assessment before-after, mapping dan coverage kontrol, evidence, exceptions, serta scan berkala. `changed=0` membuktikan idempotensi terhadap state role, bukan otomatis membuktikan CIS compliance.

## Jalur belajar singkat

1. Inventory dan `ansible.builtin.ping`.
2. Ad-hoc modules: `command`, `copy`, `package`, `service`.
3. Playbook, variables, loop, conditionals, dan facts.
4. Templates dan handlers.
5. Roles, tags, check/diff, Vault.
6. Testing dengan `ansible-lint` dan Molecule.
7. Hardening: audit, remediation, validation, exceptions, dan drift detection.

Tutorial resmi: [Start automating with Ansible](https://docs.ansible.com/projects/ansible/latest/getting_started/get_started_ansible.html), [Playbook Guide](https://docs.ansible.com/projects/ansible/latest/playbook_guide/), dan [Check/diff mode](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_checkmode.html).
