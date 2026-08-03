# Ringkasan CIS Benchmark untuk interview

## Apa itu CIS Benchmark?

CIS Benchmark adalah panduan konfigurasi aman berbasis konsensus untuk sistem operasi, cloud, database, network device, dan produk lain. Benchmark bukan scanner dan bukan pengganti risk assessment; ia adalah baseline yang harus dipilih sesuai produk **dan versinya**.

Sumber resmi:

- [CIS Benchmarks overview](https://www.cisecurity.org/cis-benchmarks-overview)
- [CIS Benchmarks FAQ](https://www.cisecurity.org/cis-benchmarks/cis-benchmarks-faq)
- [CIS Ubuntu Linux Benchmarks](https://www.cisecurity.org/benchmark/ubuntu_linux)

Pada Agustus 2026, halaman resmi CIS mencantumkan Ubuntu Linux 24.04 LTS v2.0.0 dan Ubuntu Linux 22.04 LTS v3.0.0. Selalu periksa ulang versi terbaru sebelum implementasi karena nomor kontrol dan rekomendasi dapat berubah.

## Level/profile yang perlu dipahami

- **Level 1:** baseline awal untuk mengurangi attack surface dengan dampak operasional terbatas.
- **Level 2:** defense-in-depth untuk lingkungan dengan kebutuhan keamanan tinggi; dampak operasionalnya lebih besar dan perlu pengujian ketat.
- **STIG profile:** rekomendasi yang spesifik terhadap STIG, termasuk overlap yang relevan dengan profile lain.

## Hubungan CIS dan Ansible

CIS menjawab **desired secure state**; Ansible menerapkan state itu secara repeatable ke banyak host. Alur yang baik:

1. Pastikan OS dan versi benchmark sama.
2. Pilih profile dan kontrol yang applicable.
3. Catat pengecualian beserta business justification.
4. Audit kondisi awal dan simpan evidence.
5. Implementasikan kontrol secara idempotent dalam role bertag.
6. Jalankan `--check --diff`, mulai dari canary, lalu rolling deployment.
7. Scan ulang, review failure, dan rollback bila perlu.
8. Jadwalkan audit berkala untuk menemukan configuration drift.

## Mengapa lab ini hanya “CIS-inspired”?

Implementasi CIS lengkap harus mengacu pada dokumen benchmark tertentu, mencakup kontrol automated maupun manual, diuji oleh scanner, dan memiliki evidence serta exception management. Role `security` di repo ini hanya menunjukkan pola otomasi untuk beberapa tema: audit service, authentication policy, core dump, sysctl network, cron permissions, dan SSH.

Jangan mengklaim “CIS compliant” hanya karena sebuah playbook berhasil. Compliance memerlukan scope, versi benchmark, profile, hasil assessment, coverage, exceptions, dan evidence.

## Brainstorming lanjutan

- Pisahkan role `audit` dan `remediation`; audit tidak mengubah host.
- Gunakan tag per section/control agar remediation bisa dipilih.
- Simpan secret dengan Ansible Vault, bukan plaintext di inventory.
- Tambahkan CI: syntax check, `ansible-lint`, dan Molecule.
- Uji role pada image Ubuntu 22.04 dan 24.04.
- Ekspor hasil scan ke SIEM/dashboard dan buat exception register.
- Terapkan canary + `serial` agar perubahan SSH/kernel tidak memutus seluruh fleet.
- Gunakan upstream community role hanya setelah membaca mapping, defaults, license, dan dukungan versi benchmark-nya.

## Jawaban singkat untuk interview

> CIS Benchmark memberi baseline secure configuration yang version-specific. Ansible membuat remediation konsisten, idempotent, dan scalable, tetapi keberhasilan playbook tidak otomatis berarti CIS compliant. Saya tetap membutuhkan assessment sebelum/sesudah, exception management, testing, evidence, dan rollout bertahap.

