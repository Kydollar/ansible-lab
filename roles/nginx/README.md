# Role `nginx`

Menginstal Nginx, membuat landing page berbeda per host, memvalidasi konfigurasi, lalu me-reload service melalui handler. Variabel utama:

- `nginx_listen_port` (default `80`)
- `nginx_server_name` (default `_`)
- `nginx_document_root` (default `/var/www/ansible-lab`)
