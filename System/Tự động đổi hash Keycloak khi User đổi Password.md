# TỰ ĐỘNG ĐỔI MÃ HASH TRONG ATTRIBUTE MAILCOW_PASSWORD TRÊN KEYCLOAK KHI NGƯỜI DÙNG ĐỔI PASS

## 1. Tạo API sinh Hash trên server Mailcow
### 1.1 Nguyên tắc bảo mật (bắt buộc)
- API chỉ bind private IP hoặc localhost
- Chỉ Keycloak server gọi được
- Không log plaintext
- Không lưu plaintext
- Có shared secret hoặc mTLS
- Đây là API nội bộ, không phải public service.

### 1.2 Tạo API đơn giản bằng Python Flask
- Cài môi trường trên server Mailcow
```
apt update
apt install -y python3 python3-pip
pip3 install flask
```
- Tạo file API
```
mkdir -p /opt/mailcow-hash-api
nano /opt/mailcow-hash-api/app.py
```
- Nội dung `app.py`
```
from flask import Flask, request, jsonify, abort
import subprocess
import os

app = Flask(__name__)

SHARED_SECRET = "CHANGE_ME_SECRET"

@app.route("/hash", methods=["POST"])
def hash_password():
    if request.headers.get("X-Auth-Token") != SHARED_SECRET:
        abort(401)

    data = request.get_json()
    if not data or "password" not in data:
        abort(400)

    password = data["password"]

    proc = subprocess.Popen(
        [
            "docker", "exec", "-i",
            "mailcowdockerized-dovecot-mailcow-1",
            "doveadm", "pw", "-s", "BLF-CRYPT"
        ],
        stdin=subprocess.PIPE,
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        text=True
    )

    out, err = proc.communicate(password + "\n" + password + "\n")

    if proc.returncode != 0:
        abort(500)

    hash_line = out.strip().splitlines()[-1]
    if not hash_line.startswith("{BLF-CRYPT}"):
        abort(500)

    return jsonify({"hash": hash_line})
```
- Chạy API (chỉ bind private) ; K đc bấm `Ctrl + C`
```
cd /opt/mailcow-hash-api
FLASK_RUN_HOST=127.0.0.1 FLASK_RUN_PORT=18080 flask run
```
- Mở 1 SSH khác test API:
  - Kiểm tra Port `18080` có chạy không:
  ```
  ss -lntp | grep 18080
  ```
  - Test API sinh Hash
  ```
  curl -X POST http://127.0.0.1:18080/hash \
    -H "Content-Type: application/json" \
    -H "X-Auth-Token: CHANGE_ME_SECRET" \
    -d '{"password":"abcabc"}'
  ```

## 2. Nghe sự kiện đổi Password trong Keycloak
