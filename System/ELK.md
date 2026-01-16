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
          image: docker.elastic.co/kibana/kibana:8.12.2
          container_name: kibana
          environment:
            - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
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
      curl http://localhost:9200
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
