# Role `security`

Baseline edukasi yang terinspirasi CIS untuk Ubuntu 22.04/24.04. Role ini mencakup auditd, password aging, core dump, network sysctl, permission cron, dan SSH hardening opsional.

Role ini **bukan** implementasi atau sertifikasi CIS lengkap. Selalu cocokkan kontrol dengan versi CIS Benchmark, kebutuhan aplikasi, dan exception register organisasi.

`security_harden_ssh` default `false` untuk mencegah lockout. Jalankan `--check --diff`, uji satu host dengan `--limit vm1`, lalu rollout secara bertahap.
