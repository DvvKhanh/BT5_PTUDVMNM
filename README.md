# BÀI TẬP 5 - PHÁT TRIỂN ỨNG DỤNG VỚI MÃ NGUỒN MỞ
# Họ tên: Đậu Văn Khánh
# MSSV: K225480106099
# Lớp: K58KTP
# ĐỀ BÀI
1. Lý thuyết: 
- Docker là gì? 
- Các keyword được sử dụng trong docker-compose.yml
  + Để mô tả 1 service, network, volume,...
  + Liệt kê + ý nghĩa của từ khoá đó + ví dụ minh hoạ
- Ưu điểm khi triển app sử dụng docker là gì?
- Dùng docker: tạo app, test app OK trên laptop cá nhân
  + Giờ muốn triển khai app này trên máy chủ thật ko có internet thì các bước cần làm là?

2. Thực hành áp dụng: APP MONITOR + ALERT DATA REALTIME
- Sử dụng docker compose có nhiều serivce 
- Và các thành phần cần thiết để tạo thành ứng dụng:
  + Nodered liên tục lấy dữ liệu từ nguồn nào đó (chứng khoán, thời tiết, giá vàng,...) nguồn thực tế, số liệu luôn động sau thời gian ngắn
  + Nodered lưu trữ dữ liệu vào 2 database: mariadb để lưu giá trị tức thời, lưu lịch sử vào influxdb
  + Sử dụng grafana để trực quan hoá dữ liệu: vẽ biểu đồ
  + Sử dụng nginx để làm webserver
       + Chạy 1 trang web html+js+css làm front-end
       + js: lấy dữ liệu tức thời trong mariadb qua (ajax | socket) 
           + Gọi api (api tự build bằng Flask giống bt1)
           + Api trả về giá trị tức thời trong mariadb
           + Hiển thị lên web, auto hiển thị số mới khi thay đổi
       + Sử dụng iframe để gọi grafana
       + Hiển thị biểu đồ dữ liệu lịch sử của thông số đã lưu
  + Quan sát dữ liệu lịch sử => Giá trị bất thường (VD MIỀN A..B: OK, DƯỚI A: ALERT LOW, TRÊN B: ALERT HIGH)
  + Nodered: kết hợp bot Telegram
       + Khi dữ liệu not OK, thì gửi tin nhắn từ bot => group trên telegram
       + Group đã add bot vào: (nhóm đã có 2 người), add thêm 1875746636 thành 3 người
       + Mỗi khi bot gửi dữ liệu vào nhóm: mọi member of group đều nhận đc
       + Nội dung alert: tường minh, có value gây alert
- Xuất tất cả các container ra file nén.
- Xoá mọi container đang chạy
- Load lại các container  từ file nén để khôi phục các container đã xoá.

# BÀI LÀM
# 1. Lý thuyết
## 1.1. Docker là gì? 
- Docker là:
  + Docker là một nền tảng phần mềm giúp bạn building, deploying và running ứng dụng dễ dàng hơn bằng cách sử dụng các containers (trên nền tảng ảo hóa).
  + Docker đóng gói phần mềm thành các container tiêu chuẩn hóa, chứa đựng tất cả những thứ cần thiết để phần mềm hoạt động như thư viện, công cụ hệ thống, mã nguồn và thời gian chạy. Khi cần deploy app lên bất kỳ server nào, bạn chỉ cần run container của Docker thì app của bạn sẽ được khởi chạy ngay lập tức.
  + Khi sử dụng Docker, bạn có thể dễ dàng triển khai và mở rộng quy mô ứng dụng trong bất kỳ môi trường nào, đồng thời đảm bảo rằng mã nguồn của bạn sẽ luôn chạy được một cách ổn định.

- Các thành phần chính của Docker
  + Docker Engine: dùng để tạo ra Docker image và chạy Docker container. Là thành phần chính của Docker, như một công cụ để đóng gói ứng dụng.
  + Docker Hub: dịch vụ lưu trữ giúp chứa các Docker image. Trên DockerHub có hàng ngàn public images được tạo bởi cộng đồng cho phép bạn dễ dàng tìm thấy những image mà bạn cần. Và chỉ cần pull về và sử dụng với một số config mà bạn mong muốn.
  + Docker Image: một dạng tập hợp các tệp của ứng dụng, được tạo ra bởi Docker engine. Nội dung của các Docker image sẽ không bị thay đổi khi di chuyển. Docker image được dùng để chạy các
  + Docker container. Bạn có thể tự build một image riêng cho mình hoặc sử dụng những image được chia sẽ từ cộng đồng Docker Hub. Một image sẽ được build dựa trên những chỉ dẫn của Dockerfile.
  + Docker Container: một dạng runtime của các Docker image, dùng để làm môi trường chạy ứng dụng. Bạn có thể create, start, stop, move or delete container dựa trên Docker API hoặc Docker CLI.
  + Dockerfile: là một tập tin bao gồm các chỉ dẫn để build một image.
  + Volumes: là phần dữ liệu được tạo ra khi container được khởi tạo.
  + Docker Registry: là nơi lưu trữ riêng của Docker Images. Images được push vào registry và client sẽ pull images từ registry. Có thể sử dụng registry của riêng bạn hoặc registry của nhà cung cấp như: Alibaba Cloud, AWS, Google Cloud, Microsoft Azure…
  + Docker Networking: cho phép kết nối các container lại với nhau. Kết nối này có thể trên 1 host hoặc nhiều host.
  + Docker Repository: là tập hợp các Docker Images cùng tên nhưng khác tags. VD: golang:1.11-alpine.
  + Docker Machine: tạo ra các Docker engine trên máy chủ.
  + Docker Client: là một công cụ giúp người dùng giao tiếp với Docker host thông qua command.
  + Docker Daemon: lắng nghe các yêu cầu từ Docker Client để quản lý các đối tượng như Container, Image, Network và Volumes thông qua REST API. Các Docker Daemon cũng giao tiếp với nhau để quản lý các Docker Service.
  + Docker Compose: là công cụ cho phép run app với nhiều Docker containers 1 cách dễ dàng hơn. Docker Compose cho phép bạn config các command trong file docker-compose.yml để sử dụng lại. Có sẵn khi cài Docker.
  + Docker Swarm: để phối hợp triển khai container.
  + Docker Services: là các containers trong production. Một service chỉ run 1 image nhưng nó mã hoá cách thức để run image — sử dụng port nào, bao nhiêu bản sao container run để service có hiệu năng cần thiết và ngay lập tức.
  + Docker Object: khi sử dụng docker, bạn có thể khở tạo hoặc sử các images, container, network, volumes, plugins hoặc các object khác. Những thành phần này được gọi chung là docker objects.

## 1.2. Các keyword được sử dụng trong docker-compose.yml
### Cấu trúc tổng quát:
```
version: '3.8'

services:
  app:
    ...

networks:
  ...

volumes:
  ...
```
### Version:
- Ý nghĩa: Là version docker compose mà chúng ta sử dụng. Ở đây chúng ta đang sử dụng version 3. (Lưu ý: mỗi version sẽ có sự khác nhau, version khác nhau sẽ có những option khác nhau).
- Ví dụ:
```
version: '3.8'
```
### Services:
- Ý nghĩa: Là khu vực khai báo các services cần thiết cho ứng dụng
  + nginx là container chạy nginx
  + app là container chưa source code chính ứng dụng của bạn
  + db là container chứa thông tin về database
- Ví dụ:
```
services:
  nginx:
  app:
  db:
```

### Build: 
- Ý nghĩa: Khởi tạo services bằng Dockerfile. Mặc định khi khởi tạo container sẽ khởi tạo từ Dockerfile có path được config ở context. Trường hợp trong thư mục đó có nhiều Dockerfile và bạn muốn chỉ định cụ thể sẽ khởi chạy từ Dockerfile nào thì bạn cần bổ sung thêm key dockerfile như vd dưới
- Ví dụ:
```
build:
  context: docker/nginx
  dockerfile: Dockerfile
```

### context
- Ý nghĩa: Chỉ định thư mục chứa mã nguồn để build Image.
- Ví dụ:
```
context: .
```
- Giải thích:
  + Dấu . nghĩa là thư mục hiện tại của project.
  + Ví dụ:
```
Project/
│
├── docker-compose.yml
├── app
├── docker
```
-> Docker sẽ lấy toàn bộ project làm vùng build.

### dockerfile
- Ý nghĩa: Chỉ định tên file Dockerfile cần sử dụng.
- Ví dụ:
```
dockerfile: docker/app/Dockerfile
```

### depends_on : 
- Ý nghĩa: Thiết lập container phụ thuộc vào container khác
- Ví dụ:
```
depends_on:
  - db
```
 -> Khai báo container phụ thuộc vào container có tên là db.

### port: 
- Ý nghĩa: Option này thiết lập port cho container, maping port của container với port của máy local.
- Ví dụ:
```
ports:
  - 80:80
  - 443:443
```
-> Giải thích:
```
Máy thật     Container
80     --->    80
443    --->   443
```
- Ví dụ khác:
```
ports:
  - 3000:3000
```
-> Giải thích: port 3000 là chỉ định port ở máy local, port 4000 là của container.

### env_file
- Ý nghĩa: Chỉ định file lưu trữ các biến môi trường cho container hoặc nếu không sử dụng env_file thì bạn có thể config trực tiếp từng biến môi trường vào container với key environment.
- Ví dụ:
```
env_file:
  - .env
```

### environment
- Ý nghĩa: Khai báo trực tiếp biến môi trường.
- Ví dụ:
```
environment:
      - MYSQL_ROOT_PASSWORD=${DATABASE_ROOT_PASSWORD}
      - MYSQL_DATABASE=${DATABASE_NAME}
      - MYSQL_USER=${DATABASE_USERNAME}
      - MYSQL_PASSWORD=${DATABASE_PASSWORD}
```
- Giải thích: Docker truyền các thông tin cấu hình vào MySQL.
  + Ví dụ:
```
MYSQL_DATABASE = mydb
MYSQL_USER = admin
MYSQL_PASSWORD = 123456
```

### networks
- Ý nghĩa: set network cho container.
- Ví dụ:
```
networks:
  - my_network
```

### image
- Ý nghĩa: Chỉ định Image để khởi tạo container. Khi bạn đã có sẵn 1 image rồi thì hoàn toàn có thể chạy container dựa trên image đó. (Lưu ý: khi dùng image thì key build: context.. dùng để khởi tạo container không cần thiết nữa có thể xóa đi).
- Ví dụ:
```
image: mysql:5.7
```

### restart
- Ý nghĩa:
  + restart: "no" --> defaut nó sẽ không khởi động lại container trong bâts cứ trường hợp nào
  + restart: always --> Luôn khởi động lại khi xảy ra lỗi hoặc bị stop
  + restart: on-failure --> Khởi động lại nếu xả ra lỗi
  + restart: unless-stopped --> Luôn khởi động lại container khi bị lỗi, ngoại trừ container bị stop
- Ví dụ:
```
restart: on-failure
```
- Giải thích:
  + Nếu MySQL bị lỗi và dừng đột ngột:
```
MySQL crash
 ↓
Docker tự khởi động lại
```

### volumes
- Ý nghĩa: Là option nên config, volumes cho phép mount data từ container ra máy local. Khi config option này thì mỗi lần stop container data của container đó sẽ không bị mất đi. (container có 1 đặc điểm là khi bạn stop thì những data sinh ra ở lần chạy trước sẽ bị mất đi).
- Ví dụ:
```
volumes:
  - .:/app
```
```
volumes:
  - mysql_data:/var/lib/mysql
```

### command: 
- Ý nghĩa: Giống như Dockerfile, khai báo command nào sẽ chạy khi container được chạy
- Ví dụ:
```
command: sh /scripts/command.sh
```

### Tóm tắt các keyword được sử dụng trong docker-compose.yml
```
| Keyword     | Ý nghĩa                                                                                                  |
| ----------- | -------------------------------------------------------------------------------------------------------- |
| version     | Xác định phiên bản Docker Compose được sử dụng.                                                          |
| services    | Khai báo các service (container) của ứng dụng như nginx, app và db.                                      |
| build       | Xây dựng (build) Image từ Dockerfile để tạo container.                                                   |
| context     | Chỉ định thư mục chứa mã nguồn và Dockerfile khi build Image.                                            |
| dockerfile  | Chỉ định Dockerfile cụ thể được sử dụng để build Image.                                                  |
| depends_on  | Thiết lập mối quan hệ phụ thuộc giữa các container.                                                      |
| ports       | Mapping cổng giữa máy host và container để cho phép truy cập dịch vụ.                                    |
| env_file    | Đọc các biến môi trường từ file `.env`.                                                                  |
| environment | Khai báo trực tiếp các biến môi trường cho container.                                                    |
| networks    | Thiết lập mạng giúp các container giao tiếp với nhau.                                                    |
| image       | Chỉ định Image có sẵn để khởi tạo container.                                                             |
| restart     | Thiết lập chính sách tự khởi động lại container khi xảy ra lỗi.                                          |
| volumes     | Lưu trữ và đồng bộ dữ liệu giữa container và máy host, tránh mất dữ liệu khi container dừng hoặc bị xóa. |
| command     | Chỉ định lệnh được thực thi khi container khởi động.                                                     |
```

### Ví dụ hoàn chỉnh
```
version: "3.8"

services:
  nginx:
    build:
      context: docker/nginx
      dockerfile: Dockerfile
      args:
        - HOST=${HOST}
    depends_on:
      - app
    ports:
      - 80:80
      - 443:443
    env_file:
      - .env
    networks:
     - my_network

  app:
    depends_on:
      - db
    env_file:
      - .env
    ports:
      - 3000:3000
    build:
      context: .
      dockerfile: docker/app/Dockerfile
    volumes:
      - .:/app
      - bundle_data:/bundle
    command: sh /scripts/command.sh
    stdin_open: true
    tty: true

  db:
    image: mysql:5.7
    restart: on-failure
    env_file:
      - .env
    environment:
      - MYSQL_ROOT_PASSWORD=${DATABASE_ROOT_PASSWORD}
      - MYSQL_DATABASE=${DATABASE_NAME}
      - MYSQL_USER=${DATABASE_USERNAME}
      - MYSQL_PASSWORD=${DATABASE_PASSWORD}
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
  bundle_data:
```

## 1.3. Ưu điểm khi triển app sử dụng docker là gì?
- Tiện lợi: Bình thường khi cần chạy ứng dụng chúng ta cần cài đầy đủ môi trường trên máy tính, chưa kể có sự xung đột, sự cố xảy ra với các ứng dụng. Với Docker, bạn có thể đóng gói tất cả các thành phần của ứng dụng vào một container và chạy nó trên bất kỳ máy tính nào mà không cần phải cài đặt lại môi trường.
- Dễ dàng sử dụng: Docker rất dễ cho mọi người sử dụng từ developers, systems admins, architects… nó tận dụng lợi thế của container để build, test nhanh chóng. Có thể đóng gói ứng dụng trên laptop của họ và chạy trên public cloud, private cloud… Câu thần chú là “Build once, run anywhere”.
- Tốc độ: Docker Containers tương đối nhẹ và có tốc độ rất nhanh. Bạn hoàn toàn có thể tạo và khởi chạy chỉ trong vài giây.
- Linh hoạt: Triển khai ở bất kỳ nơi đâu do sự phụ thuộc của ứng dụng vào hạ tầng OS cũng như cơ sở hạ tầng được loại bỏ.
- Môi trường chạy và khả năng mở rộng: Bạn có thể chia nhỏ những chức năng của ứng dụng thành các container riêng lẻ. Ví dụ: Database chạy trên một container và Redis cache có thể chạy trên một container khác trong khi ứng dụng Node.js lại chạy trên một cái khác nữa. Với Docker, rất dễ để liên kết các container với nhau để tạo thành một ứng dụng, làm cho nó dễ dàng scale, update các thành phần độc lập với nhau.

## 1.4. Dùng docker: tạo app, test app OK trên laptop cá nhân (Giờ muốn triển khai app này trên máy chủ thật ko có internet thì các bước cần làm là?)
Giả sử bạn đã phát triển và kiểm thử ứng dụng thành công trên laptop cá nhân bằng Docker. Bây giờ cần triển khai ứng dụng lên một máy chủ thật không có kết nối Internet thì cần thực hiện các bước sau:
### Bước 1: Build ứng dụng trên máy cá nhân
- Trên máy phát triển, build Docker Image của ứng dụng:
```
docker build -t myapp:v1 .
```
- Kiểm tra Image vừa tạo:
```
docker images
```
- Kết quả:
```
REPOSITORY   TAG   IMAGE ID
myapp        v1    xxxxxxxx
```

### Bước 2: Kiểm thử ứng dụng
- Chạy thử ứng dụng bằng Docker Compose:
```
docker compose up -d
```
- Hoặc:
```
docker run -d -p 3000:3000 myapp:v1
```
- Kiểm tra:
  + Chức năng hoạt động bình thường.
  + Kết nối Database thành công.
  + Không phát sinh lỗi.
- Sau khi xác nhận hệ thống hoạt động ổn định mới tiến hành triển khai.

### Bước 3: Lưu Docker Image thành file
- Do máy chủ không có Internet nên không thể tải Image từ Docker Hub.
- Cần xuất Image ra file:
```
docker save -o myapp.tar myapp:v1
```
- Nếu hệ thống có nhiều Image:
```
docker save -o deploy.tar nginx mysql:5.7 myapp:v1
```
- Kết quả:
```
myapp.tar
```
hoặc
```
deploy.tar
```

### Bước 4: Chuẩn bị bộ triển khai
- Tạo thư mục chứa toàn bộ dữ liệu cần mang sang máy chủ:
```
Deploy/
│
├── docker-compose.yml
├── .env
├── myapp.tar
├── scripts/
└── data/
```
- Copy thư mục này vào:
  + USB
  + Ổ cứng ngoài.
  + Mạng LAN nội bộ.

### Bước 5: Cài đặt Docker trên máy chủ
- Vì máy chủ không có Internet nên cần chuẩn bị trước bộ cài Docker.
- Ví dụ:
```
Docker Desktop Installer.exe
```
hoặc:
```
docker-ce.deb
docker-ce.rpm
```
- Sau đó cài Docker trên máy chủ.
- Kiểm tra:
```
docker --version
docker compose version
```

### Bước 6: Chép dữ liệu sang máy chủ
- Sao chép thư mục Deploy từ USB hoặc ổ cứng ngoài vào máy chủ:
```
D:\Deploy
```
- hoặc
```
/opt/deploy
```

### Bước 7: Nạp Docker Image vào máy chủ
- Di chuyển tới thư mục chứa file Image:
```
cd Deploy
```
- Import Image:
```
docker load -i myapp.tar
```
- Kiểm tra:
```
docker images
```
- Kết quả sẽ hiển thị Image vừa nạp.

### Bước 8: Khởi động hệ thống
- Nếu sử dụng Docker Compose:
```
docker compose up -d
```
- Kiểm tra container:
```
docker ps
```
- Ví dụ:
```
nginx
app
db
```
-> đều ở trạng thái Running.

### Bước 9: Kiểm tra hoạt động
- Kiểm tra log:
```
docker logs app
```
- Kiểm tra các service:
```
docker ps
```
- Truy cập ứng dụng: http://IP_Server hoặc: http://IP_Server:3000
- Nếu truy cập thành công thì việc triển khai hoàn tất.

### Quy trình tổng quát
```
Laptop cá nhân
│
├─ Viết ứng dụng
├─ Build Docker Image
├─ Test thành công
├─ docker save
└─ Copy sang USB
          │
          ▼
Máy chủ không Internet
│
├─ Cài Docker
├─ Copy source và image
├─ docker load
├─ docker compose up -d
└─ Kiểm tra hệ thống
```

# 2. Thực hành áp dụng
## 2.1. Kiến trúc hệ thống
```
Giá vàng API
      |
      v
 Node-RED
      |
      +-------------------+
      |                   |
      v                   v
 MariaDB            InfluxDB
 (realtime)         (history)
      |
      v
 Flask API
      |
      v
 Nginx
      |
      v
 HTML + JS Dashboard
      |
      +---- iframe ----+
                       |
                       v
                    Grafana

Node-RED
      |
      v
Telegram Bot
      |
      v
Telegram Group
```
## 2.2. Cấu trúc thư mục
```
gold-monitor
├── docker-compose.yml
├── backup/
├── flask-api/
│   ├── app.py
│   ├── requirements.txt
│   └── Dockerfile
└── nginx/
    ├── nginx.conf
    └── html/
        ├── index.html
        ├── script.js
        └── style.css
```
## 2.3. Tạo project
- Tạo thư muc: ```mkdir ~/gold-monitor```
- Vào thư mục: ```cd ~/gold-monitor```
- Tạo cấu trúc: ```mkdir -p flask-api nginx/html backup```
<img width="818" height="107" alt="image" src="https://github.com/user-attachments/assets/654ac430-c67b-4947-b2d2-04e6d2dfba61" />

## 2.4. Tạo docker-compose.yml
- Gõ lệnh: ```nano docker-compose.yml```
- Nội dung file:
```
services:

  mariadb:
    image: mariadb:11
    container_name: mariadb
    restart: always

    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: golddb

    ports:
      - "3306:3306"

    volumes:
      - mariadb_data:/var/lib/mysql

  influxdb:
    image: influxdb:2.7
    container_name: influxdb
    restart: always

    ports:
      - "8086:8086"

    volumes:
      - influxdb_data:/var/lib/influxdb2

  grafana:
    image: grafana/grafana
    container_name: grafana
    restart: always

    ports:
      - "3000:3000"

  nodered:
    image: nodered/node-red
    container_name: nodered
    restart: always

    ports:
      - "1880:1880"

  flask-api:
    build: ./flask-api
    container_name: flask-api

    ports:
      - "5000:5000"

    depends_on:
      - mariadb

  nginx:
    image: nginx
    container_name: nginx

    ports:
      - "80:80"

    volumes:
      - ./nginx/html:/usr/share/nginx/html
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf

    depends_on:
      - flask-api

volumes:
  mariadb_data:
  influxdb_data:
```
<img width="1478" height="754" alt="image" src="https://github.com/user-attachments/assets/eb9e5cb4-cb5c-4c11-a9ae-aa2973943b36" />

## 2.5. Xây dựng Flask API
- File app.py: ```nano flask-api/app.py```
```
from flask import Flask,jsonify
import pymysql

app = Flask(__name__)

@app.route('/api/gold')

def gold():

    conn = pymysql.connect(
        host='mariadb',
        user='root',
        password='root123',
        database='golddb'
    )

    cur = conn.cursor()

    cur.execute("""
    SELECT price
    FROM gold_price
    ORDER BY id DESC
    LIMIT 1
    """)

    row = cur.fetchone()

    return jsonify({
        "price": row[0]
    })

app.run(
    host="0.0.0.0",
    port=5000
)
```
<img width="1476" height="758" alt="image" src="https://github.com/user-attachments/assets/80fc2c81-6788-4306-819e-5fa2604214b1" />

- File requirements.txt: ```nano flask-api/requirements.txt```
```
flask
pymysql
```
<img width="1184" height="160" alt="image" src="https://github.com/user-attachments/assets/64836920-7075-48f8-8352-ef9781fe8f2b" />

- File Dockerfile: ```nano flask-api/Dockerfile```
```
FROM python:3.11

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

CMD ["python","app.py"]
```
<img width="959" height="327" alt="image" src="https://github.com/user-attachments/assets/e08da91c-0be5-4ffe-90de-7637edfcb099" />

## 2.6. Website Dashboard
- index.html: ```nano nginx/html/index.html```
```
<!DOCTYPE html>
<html>

<head>
<meta charset="UTF-8">
<title>Gold Monitor</title>
</head>

<body>

<h1>GIÁ VÀNG REALTIME</h1>

<h2 id="gold">Loading...</h2>

<hr>

<h2>LỊCH SỬ GIÁ VÀNG</h2>

<iframe
src="http://localhost:3000"
width="100%"
height="600">
</iframe>

<script src="script.js"></script>

</body>

</html>
```
<img width="1470" height="759" alt="image" src="https://github.com/user-attachments/assets/576d7224-580d-49fa-8ff3-085817cead95" />

- script.js: ```nano nginx/html/script.js```
```
function loadGold(){

fetch('/api/gold')

.then(response=>response.json())

.then(data=>{

document.getElementById("gold")
.innerHTML =
data.price + " USD";

});

}

loadGold();

setInterval(loadGold,5000);
```
<img width="1475" height="750" alt="image" src="https://github.com/user-attachments/assets/ccece9c8-2959-44b7-a6e8-34356e5861c5" />

## 2.7. Cấu hình Nginx
- Chạy lệnh: ```nano nginx/nginx.conf```
```
events {}

http {

 server {

  listen 80;

  location / {

   root /usr/share/nginx/html;
   index index.html;

  }

  location /api/ {

   proxy_pass http://flask-api:5000;

  }

 }

}
```
<img width="1473" height="756" alt="image" src="https://github.com/user-attachments/assets/9be5d570-d73d-4b20-ae62-78b76031baf2" />

## 2.7. Khởi động hệ thống
- Chạy lệnh: ```docker compose up -d```
<img width="1476" height="215" alt="image" src="https://github.com/user-attachments/assets/2bc5a351-c4b3-4944-a8f0-37b94cf2bbc2" />

- Kiểm tra: ```docker ps```
<img width="1459" height="354" alt="image" src="https://github.com/user-attachments/assets/f8fff57d-f014-4fd5-af11-7dca53bd1c94" />

## 2.8. Khởi tạo MariaDB
- Đăng nhập: ```docker exec -it mariadb mariadb -uroot -p```
- Nhập mật khẩu: root123
- Chọn database: USE golddb;
- Tạo bảng:
```
CREATE TABLE gold_price(

 id INT AUTO_INCREMENT PRIMARY KEY,

 price DOUBLE,

 created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);
```
<img width="957" height="531" alt="image" src="https://github.com/user-attachments/assets/2e3ce867-2f3a-4801-ae36-44bdea3af95f" />

- Kiểm tra: ```SHOW TABLES;```
<img width="396" height="225" alt="image" src="https://github.com/user-attachments/assets/47f60378-2af5-43b3-a58a-ac94f1b192ef" />

## 2.9. Cấu hình Node-RED
### Bước 1: Mở trình duyệt, truy cập: http://192.168.91.154:1880/
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/aa943b01-af0e-4f90-84b7-09b09ccf21a2" />

### Bước 2: Cài các nốt cần thiết
  + Nhấn ☰ bên phải -> Manage Palette -> Install -> Tìm và cài các node
  + Cài:
    + node-red-node-mysql
<img width="878" height="618" alt="image" src="https://github.com/user-attachments/assets/e692692a-b990-4992-a189-28e5e41b720d" />

+ node-red-contrib-influxdb
<img width="670" height="598" alt="image" src="https://github.com/user-attachments/assets/56c6ace9-8b92-41fc-99f3-4e0a7473bbfc" />

+ node-red-contrib-telegrambot
<img width="666" height="432" alt="image" src="https://github.com/user-attachments/assets/54482f76-97ee-4a7a-8d6f-c6e6c416fd44" />

### Bước 3: Flow lấy giá vàng
- Kéo các node:
```
Inject
   |
HTTP Request
   |
Function
   |
MySQL
```
- Cấu hình Inject:
  + Inject Node được sử dụng để kích hoạt quá trình thu thập dữ liệu tự động theo chu kỳ. Trong bài thực hành này, Inject Node được cấu hình để gửi tín hiệu kích hoạt lặp lại sau mỗi 5 giây. Mỗi lần được kích hoạt, hệ thống sẽ thực hiện việc lấy dữ liệu giá vàng mới nhất từ API và lưu vào cơ sở dữ liệu.
  + Mục đích của Inject Node là mô phỏng cơ chế thu thập dữ liệu thời gian thực (Realtime Data Collection), giúp hệ thống liên tục cập nhật các biến động của giá vàng.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0cfec98b-49fd-4d61-8e49-932250b2edce" />

- Cấu hình http request: HTTP Request Node có nhiệm vụ gửi yêu cầu HTTP đến API do Flask xây dựng.
  + Thông số cấu hình:
    + Phương thức (Method): GET
    + URL: http://flask-api:5000/api/gold
    + Return: Parsed JSON Object
  + Khi nhận được yêu cầu từ Inject Node, HTTP Request Node sẽ gọi API Flask để lấy dữ liệu giá vàng mới nhất. API Flask tiếp tục truy vấn dữ liệu từ nguồn giá vàng trực tuyến và trả về kết quả dưới dạng JSON.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b1a03ae3-20b2-4d5d-b733-ef47eb4228fa" />

- Cấu hình function:
  + Function Node được sử dụng để xử lý dữ liệu JSON nhận được từ API trước khi lưu vào cơ sở dữ liệu MariaDB.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/8a21fff1-06e9-4433-9798-576751540490" />

- Cấu hình mysql:
  + MySQL Node có nhiệm vụ kết nối đến cơ sở dữ liệu MariaDB và thực thi câu lệnh SQL do Function Node gửi tới.
  + Thông số cấu hình:
    + Host: mariadb
    + Port: 3306
    + User: root
    + Password: điền password
    + Database: golddb
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/a51c3efb-c167-4452-933e-a9d7b490327d" />

<img width="1065" height="266" alt="image" src="https://github.com/user-attachments/assets/e0423f15-bf07-4e36-9cc0-cec6fa496903" />

### Bước 4: Cấu hình InfluxDB
- Mở trình duyệt, truy cập: http://192.168.91.154:8086/
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/ee6c4a1d-c35d-43a1-94e0-9470a2a24e45" />

- Tạo tài khoản:
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0b8f667c-c4ee-4b97-b8ae-8b037c2c2e33" />

- Token -> Copy:
<img width="1920" height="1205" alt="4" src="https://github.com/user-attachments/assets/d594db72-84e4-4ab4-beca-274597292f41" />

- Cấu hình Node InfluxDB:
  + Thêm node: influxdb out
  + Cấu hình:
    + URL: http://influxdb:8086
    + Token: (token bạn đã tạo)
    + Organization: GoldMonitor
    + Bucket: gold_bucket
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/da00f757-228d-4d4b-b07b-9ac839b5a1d9" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4765393d-bd8f-455f-92c9-7ff17c7fad5a" />

- Measurement (rất quan trọng)
  + Trong node InfluxDB: Measurement = gold

👉 Đây là bảng dữ liệu trong InfluxDB

### Bước 5:
