# TẠO THÔNG BÁO REMOTE PC QUA MESH CENTRAL TRÊN TELEGRAM

## Kiểm tra Docker chạy Mesh Central
- Lệnh kiểm tra:
```
docker ps
```
- Truy cập vào container MeshCentral
```
docker exec -it meshcentral sh
cd /opt/meshcentral/meshcentral
```
- Cài công cụ
```
apk add nano jq curl
```
- Kiểm tra lệnh MeshCtrl (điền mật khẩu vào xem nó có nhận k)
```
node meshctrl.js serverinfo --url wss://localhost --loginuser administrator --loginpass 'MAT_KHAU'
```
- file script: `/opt/meshcentral/meshcentral/mesh_to_telegram.sh`
- tạo file:
```
nano /opt/meshcentral/meshcentral/mesh_to_telegram.sh
```
- Nội dung: (thay bot token, chat id, pass tk admin)
```
#!/bin/sh

BOT_TOKEN="BOT_TOKEN_CUA_BAN"
CHAT_ID="CHAT_ID_CUA_BAN"
LOGIN_USER="administrator"
LOGIN_PASS="MAT_KHAU_ADMIN_CUA_BAN"

NODE_MAP="/tmp/node_map.txt"
touch "$NODE_MAP"

send_telegram() {
  curl -s -X POST "https://api.telegram.org/bot$BOT_TOKEN/sendMessage" \
    -d "chat_id=$CHAT_ID" \
    -d "text=$1" > /dev/null
}

format_time() {
  d="$1"
  if [ -n "$d" ] && [ "$d" != "null" ]; then
    if date -d "$d" +"%d/%m/%Y %H:%M:%S" >/dev/null 2>&1; then
      date -d "$d" +"%d/%m/%Y %H:%M:%S"
      return
    fi
  fi
  date +"%d/%m/%Y %H:%M:%S"
}

get_action() {
  msg="$1"
  lower=$(echo "$msg" | tr '[:upper:]' '[:lower:]')

  if echo "$lower" | grep -q "started"; then
    prefix="Started"
  elif echo "$lower" | grep -q "ended"; then
    prefix="Ended"
  else
    prefix="Unknown"
  fi

  if echo "$lower" | grep -q "desktop"; then
    echo "$prefix Desktop"
    return
  fi

  if echo "$lower" | grep -q "terminal"; then
    echo "$prefix Terminal"
    return
  fi

  if echo "$lower" | grep -q "file management"; then
    echo "$prefix Files"
    return
  fi

  if echo "$lower" | grep -q "remote files"; then
    echo "$prefix Files"
    return
  fi

  if echo "$lower" | grep -q "remote desktop"; then
    echo "$prefix Desktop"
    return
  fi

  echo "$prefix Other"
}

extract_from() {
  echo "$1" | sed -n 's/.*from \([0-9.]*\).*/\1/p'
}

extract_to() {
  echo "$1" | sed -n 's/.*to \([0-9.]*\).*/\1/p'
}

get_machine_name() {
  raw_nodeid="$1"
  nodeid=$(echo "$raw_nodeid" | sed 's#^node//##')

  machine=$(grep -F -- "$nodeid|" "$NODE_MAP" | tail -n1 | cut -d'|' -f2)

  if [ -z "$machine" ]; then
    machine=$(node meshctrl.js listdevices \
      --url wss://localhost \
      --loginuser "$LOGIN_USER" \
      --loginpass "$LOGIN_PASS" 2>/dev/null | \
      awk -F',' -v nid="$nodeid" '
      /^"/ {
        gsub(/"/,"",$1);
        gsub(/"/,"",$2);
        gsub(/^ +| +$/,"",$1);
        gsub(/^ +| +$/,"",$2);
        if ($1 == nid) print $2
      }' | head -n1)

    if [ -n "$machine" ] && [ "$machine" != "null" ]; then
      grep -Fv -- "$nodeid|" "$NODE_MAP" > "${NODE_MAP}.tmp" 2>/dev/null
      mv "${NODE_MAP}.tmp" "$NODE_MAP"
      echo "$nodeid|$machine" >> "$NODE_MAP"
    fi
  fi

  if [ -z "$machine" ] || [ "$machine" = "null" ]; then
    machine="Unknown"
  fi

  echo "$machine"
}

preload_devices() {
  node meshctrl.js listdevices \
    --url wss://localhost \
    --loginuser "$LOGIN_USER" \
    --loginpass "$LOGIN_PASS" 2>/dev/null | \
    awk -F',' '/^"/ {
      gsub(/"/,"",$1);
      gsub(/"/,"",$2);
      gsub(/^ +| +$/,"",$1);
      gsub(/^ +| +$/,"",$2);
      print $1 "|" $2
    }' > "$NODE_MAP"
}

preload_devices

buffer=""

node meshctrl.js showevents \
  --url wss://localhost \
  --loginuser "$LOGIN_USER" \
  --loginpass "$LOGIN_PASS" | \
while IFS= read -r line
do
  buffer="$buffer$line"

  open=$(echo "$buffer" | grep -o '{' | wc -l)
  close=$(echo "$buffer" | grep -o '}' | wc -l)

  if [ "$open" -gt 0 ] && [ "$open" -eq "$close" ]; then
    json="$buffer"
    buffer=""

    echo "$json" | jq . >/dev/null 2>&1 || continue

    action=$(echo "$json" | jq -r '.event.action // empty')

    if [ "$action" = "changenode" ]; then
      nodeid=$(echo "$json" | jq -r '.event.nodeid')
      name=$(echo "$json" | jq -r '.event.node.name // empty')
      if [ -n "$name" ] && [ "$name" != "null" ]; then
        clean_nodeid=$(echo "$nodeid" | sed 's#^node//##')
        grep -Fv -- "$clean_nodeid|" "$NODE_MAP" > "${NODE_MAP}.tmp" 2>/dev/null
        mv "${NODE_MAP}.tmp" "$NODE_MAP"
        echo "$clean_nodeid|$name" >> "$NODE_MAP"
      fi
      continue
    fi

    if [ "$action" != "relaylog" ]; then
      continue
    fi

    msg=$(echo "$json" | jq -r '.event.msg // empty')
    user=$(echo "$json" | jq -r '.event.username // "unknown"')
    nodeid=$(echo "$json" | jq -r '.event.nodeid // empty')
    event_time=$(echo "$json" | jq -r '.event.time // empty')

    echo "$msg" | grep -q "Device Powered On" && continue
    echo "$msg" | grep -Ei "desktop|terminal|file management|remote" >/dev/null || continue

    machine=$(get_machine_name "$nodeid")
    from=$(extract_from "$msg")
    to=$(extract_to "$msg")
    time=$(format_time "$event_time")
    action_name=$(get_action "$msg")

    [ -z "$from" ] && from="Unknown"
    [ -z "$to" ] && to="Unknown"

    text="Time: $time
User: $user
Machine: $machine
Action: $action_name
From: $from
To: $to"

    echo "SEND:"
    echo "$text"
    echo "----------------------"

    send_telegram "$text"
  fi
done
```
- Cấp quyền chạy
```
chmod +x /opt/meshcentral/meshcentral/mesh_to_telegram.sh
```
- Xoá cache map cũ
```
rm -f /tmp/node_map.txt
```
- Chạy script:
```
cd /opt/meshcentral/meshcentral
./mesh_to_telegram.sh
```
