# ELK

## Cài đặt ELK Docker trên Ubuntu
  - Cài Docker & Docker Compose:
    - Cài Docker Engine:   
      ```
      sudo apt update
      sudo apt install -y ca-certificates curl gnupg
      sudo install -m 0755 -d /etc/apt/keyrings
      
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      
      echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
        https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $VERSION_CODENAME) stable" \
        | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      
      sudo apt update
      sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      ```
    - Nếu bị lỗi:
      ```
      Err:1 http://vn.archive.ubuntu.com/ubuntu jammy/universe amd64 pigz amd64 2.6-1 504 Gateway Timeout [IP: 113.161.78.134 80]
      Err:2 http://vn.archive.ubuntu.com/ubuntu jammy/main amd64 libslirp0 amd64 4.6.1-1build1 504 Gateway Timeout [IP: 113.161.78.134 80]
      Err:3 http://vn.archive.ubuntu.com/ubuntu jammy/universe amd64 slirp4netns amd64 1.0.1-2 504 Gateway Timeout [IP: 113.161.78.134 80]
      E: Failed to fetch http://vn.archive.ubuntu.com/ubuntu/pool/universe/p/pigz/pigz_2.6-1_amd64.deb 504 Gateway Timeout [IP: 113.161.78.134 80] E: Failed to fetch http://vn.archive.ubuntu.com/ubuntu/pool/main/libs/libslirp/libslirp0_4.6.1-1build1_amd64.deb 504 Gateway Timeout [IP: 113.161.78.134 80]
      E: Failed to fetch http://vn.archive.ubuntu.com/ubuntu/pool/universe/s/slirp4netns/slirp4netns_1.0.1-2_amd64.deb 504 Gateway Timeout [IP: 113.161.78.134 80]
      E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
      ```
      - Mở file cấu hình nguồn phần mềm:
        ```
        sudo nano /etc/apt/sources.list
        ```
      - Thay tất cả các dòng bắt đầu bằng `http://vn.archive.ubuntu.com/ubuntu/` `thành http://archive.ubuntu.com/ubuntu/` (bỏ `vn.` ở đầu url đi)
      - Lưu file và chạy lại lệnh cập nhật:
        ```
        sudo apt update
        ```
      - Cập nhật lại gói với tùy chọn:
        ```
        sudo apt update --fix-missing
        ```
      - Cập nhật hệ thống:
        ```
        sudo apt upgrade
        ```
    - Test Docker:
      ```
      docker version
      docker compose version
      ```
  - Tăng giới hạn vm.max_map_count:
    ```
    sudo sysctl -w vm.max_map_count=262144
    echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
    ```
  - Tạo thư mục dự án ELK:
    ```
    mkdir -p ~/elk-docker
    cd ~/elk-docker
    ```
  - Tạo file docker compose
    - Tạo file `docker-compose.yml`
      ```
      nano docker-compose.yml
      ```
    - Dán nội dung này vào:
      ```
      services:
        elasticsearch:
          image: docker.elastic.co/elasticsearch/elasticsearch:9.2.4
          container_name: elasticsearch
          environment:
            - discovery.type=single-node
            - xpack.security.enabled=false
            - ES_JAVA_OPTS=-Xms1g -Xmx1g
          ulimits:
            memlock:
              soft: -1
              hard: -1
          volumes:
            - esdata:/usr/share/elasticsearch/data
          ports:
            - "9200:9200"
          networks:
            - elk
      
        kibana:
          image: docker.elastic.co/kibana/kibana:9.2.4
          container_name: kibana
          environment:
            - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
            - XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=0123456789abcdef0123456789abcdef
            - XPACK_SECURITY_ENCRYPTIONKEY=abcdef0123456789abcdef0123456789
            - XPACK_REPORTING_ENCRYPTIONKEY=11223344556677889900aabbccddeeff
          ports:
            - "5601:5601"
          depends_on:
            - elasticsearch
          networks:
            - elk
      
        logstash:
          image: docker.elastic.co/logstash/logstash:9.2.4
          container_name: logstash
          ports:
            - "5044:5044"
            - "9600:9600"
          volumes:
            - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro
          depends_on:
            - elasticsearch
          networks:
            - elk
      
      volumes:
        esdata:
      
      networks:
        elk:
          driver: bridge
      ```
  - Tạo cấu hình Logstash cơ bản
    - Tạo file `logstash.conf `:
      ```
      nano logstash.conf
      ```
    - Dán nội dung sau:
      ```
      input {
        beats {
          port => 5044
        }
      }
      
      output {
        elasticsearch {
          hosts => ["http://elasticsearch:9200"]
          index => "logs-%{+YYYY.MM.dd}"
        }
        stdout { codec => rubydebug }
      }
      ```
  - Start ELK:
      ```
      docker compose up -d
      ```
    - Xem container
      ```
      docker ps
      ```
    - Xem log nếu lỗi:
      ```
      docker logs -f elasticsearch
      docker logs -f kibana
      docker logs -f logstash
      ```
  - Kiểm tra chạy OK chưa:
    - Elasticsearch:
      ```
      curl -u elastic:Nguyenvantu1102@ http://localhost:9200
      ```
    - Kibana: Mở trình duyệt: `http://localhost:5601`
  - Mở firewall (nếu dùng UFW)
    ```
    sudo ufw allow 5601
    sudo ufw allow 9200
    sudo ufw allow 5044
    ```
    
---
## Cài Filebeat trên chính máy ELK server:
  - Cài đặt Filebeat:
    ```
    sudo apt-get update
    sudo apt-get install wget apt-transport-https
    wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
    sudo sh -c 'echo "deb https://artifacts.elastic.co/packages/9.x/apt stable main" > /etc/apt/sources.list.d/elastic-9.x.list'
    sudo apt-get update
    sudo apt-get install filebeat
    ```
  - Cấu hình Filebeat:
    ```
    sudo nano /etc/filebeat/filebeat.yml
    ```
    - Cấu hình Elasticsearch output: Tìm phần cấu hình `output.elasticsearch`
      ```
      output.elasticsearch:
        hosts: ["http://localhost:9200"]
      ```
    - Tìm phần `filebeat.inputs` và chỉnh sửa:
      ```
      filebeat.inputs:
      - type: log
        enabled: true
        paths:
      - /var/log/*.log  # Theo dõi tất cả các file log trong thư mục /var/log
      ```
  - Bật các module của Filebeat:
    ```
    sudo filebeat modules enable system
    ```
  - Cài đặt và chạy Filebeat
    - Cài đặt các chỉ mục 
      ```
      sudo filebeat setup
      ```
    - Khởi động Filebeat
      ```
      sudo systemctl start filebeat
      sudo systemctl enable filebeat
      ```
    - Kiểm tra trạng thái của Filebeat:
      ```
      sudo systemctl status filebeat
      ```
  - 

---
## Cài đặt Filebeat trên máy khác
  - Cài đặt Filebeat:
    ```
    sudo apt-get update
    sudo apt-get install wget apt-transport-https
    wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
    sudo sh -c 'echo "deb https://artifacts.elastic.co/packages/9.x/apt stable main" > /etc/apt/sources.list.d/elastic-9.x.list'
    sudo apt-get update
    sudo apt-get install filebeat
    ```
  - Cấu hình Filebeat
    - Mở file cấu hình Filebeat:
      ```
      sudo nano /etc/filebeat/filebeat.yml
      ```
    - Cấu hình Output Elasticsearch: Trong file cấu hình, bạn cần sửa đổi phần `output.elasticsearch`
      ```
      output.elasticsearch:
        hosts: ["http://172.16.20.53:9200"]
      ```
    - Cấu hình Input để theo dõi log: Trong file cấu hình, bạn cần sửa đổi phần `filebeat.inputs`
      ```
      filebeat.inputs:
      - type: log
        enabled: true
        paths:
          - /var/log/*.log  # Thư mục chứa log cần theo dõi
      ```
    - Bật các module cần thiết
      ```
      sudo filebeat modules enable system
      ```
  - Khởi động và kiểm tra Filebeat:
    - Tải các chỉ mục vào Elasticsearch 
      ```
      sudo filebeat setup
      ```
    - Khởi động Filebeat
      ```
      sudo systemctl start filebeat
      sudo systemctl enable filebeat
      ```
    - Kiểm tra trạng thái của Filebeat
      ```
      sudo systemctl status filebeat
      ```
---
## Cài ELK trên pfSense
  - Mở pfSense:
  ![Ảnh 1](?raw=1)
  ![Ảnh 2](?raw=1)
  ![Ảnh 3](?raw=1)
  ![Ảnh 4](?raw=1)
  - Sửa lại cấu hình `logstash`:
    - Mở file  
    ```
    cd ~/elk-docker
    nano logstash.conf
    ```
    - Sửa cấu hình:
      ```
      input {
        beats {
          port => 5044
        }
      
        udp {
          port => 514
          type => "pfsense"
        }
      }
      
      filter {
        if [type] == "pfsense" {
          mutate {
            add_tag => ["pfsense"]
          }
        }
      }
      
      output {
        if [type] == "pfsense" {
          elasticsearch {
            hosts => ["http://elasticsearch:9200"]
            index => "pfsense-%{+YYYY.MM.dd}"
          }
        } else {
          elasticsearch {
            hosts => ["http://elasticsearch:9200"]
            index => "logs-%{+YYYY.MM.dd}"
          }
        }
      
        stdout { codec => rubydebug }
      }
      ```
    - Mở thêm port 514 lấy log từ pfSense trong `docker-compose.yml`:
      ```
      nano docker-compose.yml
      ```
      ```
      services:
        elasticsearch:
          image: docker.elastic.co/elasticsearch/elasticsearch:9.2.4
          container_name: elasticsearch
          environment:
            - discovery.type=single-node
            - xpack.security.enabled=false
            - ES_JAVA_OPTS=-Xms1g -Xmx1g
          ulimits:
            memlock:
              soft: -1
              hard: -1
          volumes:
            - esdata:/usr/share/elasticsearch/data
          ports:
            - "9200:9200"
          networks:
            - elk
      
        kibana:
          image: docker.elastic.co/kibana/kibana:9.2.4
          container_name: kibana
          environment:
            - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
            - XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=0123456789abcdef0123456789abcdef
            - XPACK_SECURITY_ENCRYPTIONKEY=abcdef0123456789abcdef0123456789
            - XPACK_REPORTING_ENCRYPTIONKEY=11223344556677889900aabbccddeeff
          ports:
            - "5601:5601"
          depends_on:
            - elasticsearch
          networks:
            - elk
      
        logstash:
          image: docker.elastic.co/logstash/logstash:9.2.4
          container_name: logstash
          ports:
            - "5044:5044"
            - "9600:9600"
            - "514:514/udp"          # mở port 514 nhận log
          volumes:
            - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro
          depends_on:
            - elasticsearch
          networks:
            - elk
      
      volumes:
        esdata:
      
      networks:
        elk:
          driver: bridge
      ```
    - Restart docker:
      ```
      docker compose up -d
      docker compose restart logstash
      ```
    - Xem log:
      ```
      docker logs -f logstash
      ```
---
## Xem log trên Kibana:
  - Truy cập Kibana qua giao diện web: `172.16.20.53:5601` (ip máy : port 5601)
  ![Ảnh 5](?raw=1)
  - Xem log máy Ubuntu qua agent Filebeat:
  ![Ảnh 6](?raw=1)
  - Xem log pfSense:
  ![Ảnh 7](?raw=1)

---
## Cảnh báo log -rm trong file log `cat /var/log/cmd.log`
  - Mở `Filebeat` sửa `input`:
    - Mở `Filebeat`:
      ```
      sudo nano /etc/filebeat/filebeat.yml
      ```
    - Thêm input:
      ```
      - type: filestream
        id: cmdlog
        enabled: true
        paths:
          - /var/log/cmd.log
        fields:
          log_type: cmdlog
        fields_under_root: true
      ```
    - Restart:
      ```
      sudo systemctl restart filebeat
      ```
    - Test:
      ```
      sudo filebeat test config
      sudo filebeat test output
      ```
    - Kiểm tra log:
      ```
      curl -s http://localhost:9200/_cat/indices?v | grep filebeat
      ```
    - 
  - Sửa file Docker-compose:
    - mở file compose:
      ```
      nano docker-compose.yml
      ```
    - Tìm service `kibana`, thêm vào phần `enviroment:` dòng:
      ```
      - XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY=0123456789abcdef0123456789abcdef
      ```
    - Thoát và lưu; Restart kibana:
      ```
      docker compose restart kibana
      ```
  - Tạo BOT Telegram:
    - Tạo Bot:
    ![Ảnh 8](?raw=1)
    - Lấy Chat ID
      - Cá nhân:
        ![Ảnh 9](?raw=1)
      - Group:
        - Tạo Group Telegram
        - Add Bot
        - Cho Bot quyền Admin Group
        - Nhắn 1 tin nhắn vào Group
        - Mở trình duyệt gõ (thay bot Token vào) để lấy chat id: 
          ```
          https://api.telegram.org/bot<TOKEN>/getUpdates
          ```
          ![Ảnh 10](?raw=1)
  - Tạo Connector Bot Telegram vs Kibana:
    ![Ảnh 12.5](?raw=1)
    ![Ảnh 13](?raw=1)
    ![Ảnh 14](?raw=1)
    ![Ảnh 15](?raw=1)
    ![Ảnh 16](?raw=1)  
  - Tạo Rule bắt log trên Kibana
  ![Ảnh 11](?raw=1)
  ![Ảnh 12](?raw=1)
  ![Ảnh 17](?raw=1)
  ![Ảnh 18](?raw=1)
  - 

