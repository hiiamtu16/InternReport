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
          image: docker.elastic.co/elasticsearch/elasticsearch:8.12.2
          container_name: elasticsearch
          environment:
            - discovery.type=single-node
            - xpack.security.enabled=true  
            - xpack.security.authc.apiKey.enabled=true  
            - ES_JAVA_OPTS=-Xms1g -Xmx1g
            - ELASTIC_PASSWORD=Nguyenvantu1102@
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
          image: docker.elastic.co/kibana/kibana:8.12.2
          container_name: kibana
          environment:
            - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
            - ELASTICSEARCH_USERNAME=elastic  
            - ELASTICSEARCH_PASSWORD=Nguyenvantu1102@
          ports:
            - "5601:5601"
          depends_on:
            - elasticsearch
          networks:
            - elk
      
        logstash:
          image: docker.elastic.co/logstash/logstash:8.12.2
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
          user => "elastic"  # User elastic (hoặc user đã tạo trong Elasticsearch)
          password => "your_password_here"  # Mật khẩu của user elastic
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
##
