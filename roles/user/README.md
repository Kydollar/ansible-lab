# Role `user`

Membuat akun administrator yang konsisten pada seluruh server Ubuntu. Variabel utama:

- `managed_users`: daftar user, group, shell, deskripsi, dan optional `authorized_keys`.
- `user_manage_passwordless_sudo`: membuat aturan sudo tervalidasi; default `false`.

Role menggunakan modul `user` dan `copy`, sehingga deklaratif dan idempotent.
