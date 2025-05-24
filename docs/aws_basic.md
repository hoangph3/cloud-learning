
# 🧠 Tổng Quan Kiến Thức AWS Cơ Bản

---

## 1. 🏗️ Hạ Tầng (Infrastructure)

- **Region**: Vùng địa lý chứa nhiều **Availability Zone (AZ)**, mỗi AZ gồm một hoặc nhiều **Data Center (DC)**.
- **AWS CloudFront**:
  - Dịch vụ CDN của AWS.
  - Các **Edge Location** được đặt gần người dùng giúp tăng tốc truy cập.
- **AWS Direct Connect**:
  - Kết nối trực tiếp từ **on-premises** đến AWS.
  - Cải thiện tốc độ và độ ổn định.
- **VPC (Virtual Private Cloud)**:
  - Một mạng riêng ảo trong AWS.
  - Có thể chứa nhiều AZ kết nối với nhau.
  - Tách biệt với các mạng khác trong AWS.

---

## 2. 💻 Tương Tác Với AWS

### 2.1. 🌐 AWS Console

- Giao diện web trực tuyến để quản lý các dịch vụ AWS.

### 2.2. 🧑‍💻 AWS CLI (Command Line Interface)

- Cài đặt trên máy local, dùng để thao tác với AWS qua dòng lệnh.

```bash
aws --version                                      # Kiểm tra phiên bản AWS CLI
aws configure                                      # Cấu hình Access Key, Secret Key và Region
aws s3 ls                                          # Liệt kê các S3 bucket
aws s3 mb s3://hoangph3-bucket-2025                # Tạo một bucket
aws s3 rm s3://hoangph3-bucket-2025 --recursive    # Xóa tất cả dữ liệu trong bucket
aws s3 rb s3://hoangph3-bucket-2025                # Xóa bucket
```

### 2.3. 📦 AWS SDK

- Giao diện lập trình để tương tác với AWS thông qua các ngôn ngữ như Python, Java, JavaScript...
- Ví dụ: sử dụng **Boto3** trong Python để làm việc với S3, EC2,...

---

## 3. 🔐 IAM (Identity and Access Management)

### 3.1. Quản lý truy cập

- Dùng tài khoản root để tạo:
  - **IAM User**
  - **Group**
  - **Role**
  - **Policy**

### 3.2. Chính sách (Policy)

- Có 2 loại chính sách:

#### 🧾 Identity Policy

- Gắn cho **User** hoặc **Group**.
- Xác định người đó được phép làm gì với tài nguyên nào.

#### 📦 Resource Policy

- Gắn trực tiếp vào **resource** như S3 bucket.
- Xác định **ai** (principal) được quyền làm gì với tài nguyên đó.

📌 **Sử dụng công cụ**: [AWS Policy Generator](https://awspolicygen.s3.amazonaws.com/policygen.html) để tạo JSON policy.

### 3.3. Role (Vai trò)

- Cấp quyền **tạm thời** cho User hoặc AWS Service.
- Không cần thêm vào group.
- Có thể:
  - Gán policy cho role.
  - Gán role cho user ở account khác.
  - Gán role cho service như EC2, Lambda, S3.

📌 **Ví dụ**: Gán IAM Role cho EC2 instance → instance sẽ có quyền được xác định trong role (như truy cập S3, quản lý EC2 khác).

---

## 4. 🌐 Mạng (Networking)

### 4.1. VPC và Subnet

- Một **VPC** có thể chứa nhiều **subnet**:
  - **Public Subnet**: Có **Route Table** kết nối với **Internet Gateway**.
  - **Private Subnet**: Không kết nối trực tiếp ra internet.

### 4.2. 🛡️ NACL (Network Access Control List)

- Áp dụng ở **mức subnet**.
- **Stateless**: inbound và outbound cần cấu hình riêng.
- Rule có **thứ tự** — áp dụng từ trên xuống.
- Hỗ trợ cả `allow` và `deny`.

### 4.3. 🔒 Security Group

- Áp dụng ở **mức instance** (ví dụ: EC2).
- **Stateful**: Cho phép inbound sẽ tự động cho phép outbound tương ứng.
- **Không có deny**, chỉ có `allow`.
- **Không có thứ tự** rule — kiểm tra toàn bộ.

---

## 5. S3, EBS và EFS

### 5.1. S3 (Simple Storage Service)

S3 là dịch vụ lưu trữ đối tượng (object storage) – giống như một “ổ đĩa không giới hạn” trong cloud. Dữ liệu được lưu thành object trong các bucket.

Tính năng nổi bật:
- Rất bền bỉ (99.999999999% durability).
- Lưu trữ hình ảnh, video, file tài liệu, bản backup...
- Có thể public để dùng như CDN, hoặc private cho nội bộ.
- Hỗ trợ versioning, encryption, lifecycle policies (tự động archive/delete)...

Trường hợp sử dụng:
- Lưu trữ dữ liệu tĩnh: hình ảnh, log, tài liệu, media,...
- Làm backend cho ứng dụng mobile/web.
- Phân phối nội dung (CDN với CloudFront).

### 5.2. EBS (Elastic Block Store)

EBS là ổ đĩa cứng gắn ngoài cho EC2 instance. Nó hoạt động tương tự ổ cứng SSD/HDD trên máy tính, nhưng có thể tách rời.

Tính năng nổi bật:
- Có thể attach/detach với EC2.
- Có các loại lưu trữ: gp3, io2, sc1... phù hợp cho từng workload.
- Hỗ trợ snapshot để backup, restore.

Trường hợp sử dụng:
- Lưu trữ dữ liệu cho máy chủ EC2 (OS, database, ứng dụng...) - vì nó có tốc độ nhanh nhất.
- Dữ liệu cần hiệu suất cao và truy cập theo block (ví dụ: đọc/ghi ngẫu nhiên).

Ghi nhớ:
- EBS chỉ gắn với một EC2 tại một thời điểm (trừ loại io2 Multi-Attach).

### 5.3. EFS (Elastic File System)

EFS là dịch vụ file storage dùng giao thức NFS, cho phép nhiều EC2 instance truy cập cùng một thư mục như chia sẻ file.

Tính năng nổi bật:
- Tự động mở rộng/thu hẹp theo dung lượng sử dụng.
- Dễ dàng chia sẻ giữa nhiều EC2 trong cùng VPC hoặc region.
- Có thể mount vào Linux EC2 như một ổ đĩa bình thường.

Trường hợp sử dụng:
- Ứng dụng cần chia sẻ dữ liệu giữa nhiều EC2 (web server, CMS...).
- Lưu trữ cấu hình, scripts, hoặc dữ liệu người dùng trong môi trường multi-instance.

### 5.4. 🔁 So sánh nhanh
| Dịch vụ | Kiểu lưu trữ | Gắn với       | Truy cập                | Dùng cho                    | Mở rộng |
| ------- | ------------ | ------------- | ----------------------- | --------------------------- | ------- |
| **S3**  | Object       | Không cần EC2 | API / HTTPS             | Hình ảnh, backup, file tĩnh | Tự động |
| **EBS** | Block        | 1 EC2         | Local (mount trực tiếp) | OS, DB, ứng dụng            | Manual  |
| **EFS** | File (NFS)   | Nhiều EC2     | Mount NFS               | Dữ liệu chia sẻ, logs       | Tự động |


✅ Khi nào dùng cái nào?
- 🧾 S3: Khi bạn cần lưu file tĩnh, không cần mount vào máy ảo.
- 🔧 EBS: Khi bạn chạy EC2 và cần ổ đĩa cho OS hoặc DB có tốc độ cao.
- 📂 EFS: Khi nhiều EC2 cần dùng chung một ổ đĩa/mount point.

## 6. EC2, ECS, EKS và Lambda

### 6.1. 🖥️ EC2 (Elastic Compute Cloud)

EC2 là dịch vụ cung cấp máy chủ ảo (Virtual Machines) chạy trong AWS cloud.

Tương tự như bạn thuê một server vật lý, nhưng bạn có thể dễ dàng cấu hình, khởi động, tắt hoặc mở rộng nó chỉ bằng vài thao tác.

Tính năng nổi bật:
- Tùy chọn cấu hình linh hoạt (CPU, RAM, dung lượng ổ đĩa...).
- Hỗ trợ nhiều hệ điều hành: Linux, Windows, Amazon Linux, Ubuntu, v.v.
- Có thể gắn thêm EBS (Elastic Block Store) để lưu trữ lâu dài.

Trường hợp sử dụng:
- Host website, ứng dụng backend, API server, game server, v.v.

### 6.2. 📦 ECS (Elastic Container Service)

ECS là dịch vụ quản lý container cho phép bạn chạy và scale các ứng dụng dạng container (như Docker) trên một cụm tài nguyên do bạn kiểm soát.

Tính năng nổi bật:

- Hỗ trợ chạy container bằng:
  - EC2 launch type: container chạy trên các EC2 instance do bạn quản lý.
  - Fargate launch type: không cần quản lý server — AWS lo toàn bộ phần hạ tầng (serverless container).
- Tích hợp dễ dàng với ALB, IAM, CloudWatch, ECR (Elastic Container Registry)...

Trường hợp sử dụng:
- Triển khai microservices sử dụng Docker.
- Tự động scale ứng dụng theo nhu cầu.

### 6.3. ☸️ EKS (Elastic Kubernetes Service)

EKS là dịch vụ quản lý Kubernetes của AWS.

Cho phép bạn triển khai các ứng dụng container hóa theo chuẩn Kubernetes mà không cần tự dựng cluster từ đầu.

Tính năng nổi bật:
- Là Kubernetes "chuẩn", tương thích 100% với upstream Kubernetes.
- AWS quản lý control plane (API server, etcd), bạn chỉ lo phần worker nodes.
- Có thể kết hợp với Fargate để chạy pods mà không cần quản lý EC2.

Trường hợp sử dụng:
- Khi team bạn đã dùng Kubernetes hoặc cần orchestration phức tạp.
- Dự án quy mô lớn, đa dịch vụ, cần CI/CD, RBAC, service mesh...

### 6.4. ⚡ Lambda (AWS Lambda)

Lambda là dịch vụ serverless cho phép bạn chạy code mà không cần quản lý server.

Bạn chỉ viết function, upload lên Lambda, AWS lo hết phần hạ tầng và scale.

Tính năng nổi bật:
- Tính phí theo thời gian chạy và số lần gọi (rất tiết kiệm chi phí).
- Tự động scale cực nhanh.
- Hỗ trợ nhiều ngôn ngữ: Python, Node.js, Java, Go, v.v.
- Tích hợp tốt với S3, DynamoDB, API Gateway, EventBridge...

Trường hợp sử dụng:
- Xử lý backend nhỏ, automation, cron job, API nhỏ, xử lý sự kiện (event-driven).

### 6.5. 🔁 So sánh nhanh

| Dịch vụ    | Mô hình             | Quản lý server                                    | Dùng cho                           | Độ phức tạp |
| ---------- | ------------------- | ------------------------------------------------- | ---------------------------------- | ----------- |
| **EC2**    | VM                  | Tự quản lý                                        | Web server, ứng dụng backend       | ⭐⭐          |
| **ECS**    | Container           | Có thể tự quản lý (EC2) hoặc serverless (Fargate) | Microservices                      | ⭐⭐⭐         |
| **EKS**    | Kubernetes          | Phần control plane do AWS quản lý                 | Hệ thống lớn cần orchestration     | ⭐⭐⭐⭐        |
| **Lambda** | Serverless Function | Không quản lý                                     | Event-driven, tự động hóa, API nhỏ | ⭐           |


## 7. 🗄️ Tổng Quan Dịch Vụ Database AWS

AWS cung cấp nhiều dịch vụ cơ sở dữ liệu phù hợp với từng loại ứng dụng: từ SQL truyền thống đến NoSQL hiện đại.

### 7.1. 🔢 RDS (Relational Database Service)

**Mô tả**:
- Dịch vụ CSDL quan hệ được quản lý hoàn toàn.
- Hỗ trợ các engine: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server.

**Tính năng**:
- Tự động backup, restore, patch.
- Hỗ trợ Multi-AZ, read replica.
- Dễ dàng scale và monitoring.

**Khi nào dùng**:
- Ứng dụng web truyền thống cần SQL database.

---

### 7.2. 🌟 Aurora (Amazon Aurora)

**Mô tả**:
- Biến thể cao cấp của RDS, tương thích MySQL & PostgreSQL.
- Hiệu năng cao gấp 3–5 lần so với RDS.

**Tính năng**:
- Tự động mở rộng dung lượng.
- Tách biệt storage và compute.
- Có phiên bản Aurora Serverless.

**Khi nào dùng**:
- Khi cần hiệu năng cao hơn RDS, độ tin cậy tốt, chi phí hợp lý.

---

### 7.3. ⚡ DynamoDB

**Mô tả**:
- Dịch vụ NoSQL key-value và document, hoàn toàn serverless.
- Hiệu suất cực cao, độ trễ cực thấp.

**Tính năng**:
- Auto scaling, backup, global tables.
- Hỗ trợ TTL, DynamoDB Streams.

**Khi nào dùng**:
- App mobile, IoT, game, session store...

---

### 7.4. 📄 DocumentDB (MongoDB-compatible)

**Mô tả**:
- Dịch vụ NoSQL document store tương thích với MongoDB.
- Lưu trữ document JSON.

**Tính năng**:
- Quản lý hoàn toàn, bảo mật tốt.
- Dễ migrate từ MongoDB truyền thống.

**Khi nào dùng**:
- App đang dùng MongoDB hoặc dữ liệu dạng document.

---

### 7.5. 🚀 ElastiCache (Redis / Memcached)

**Mô tả**:
- Dịch vụ cache in-memory giúp tăng tốc ứng dụng.
- Hỗ trợ Redis và Memcached.

**Tính năng**:
- Giảm độ trễ truy vấn database.
- Hỗ trợ pub/sub messaging (Redis).
- Dùng cho session store hoặc caching layer.

**Khi nào dùng**:
- Cần cache dữ liệu hoặc session, tăng hiệu năng.

### ✅ So sánh nhanh

| Dịch vụ       | Loại     | Mô hình       | Đặc điểm chính                   | Khi nào dùng                         |
|---------------|----------|---------------|----------------------------------|--------------------------------------|
| **RDS**       | SQL      | Managed       | SQL chuẩn, dễ triển khai         | Web app, ERP, CRM                    |
| **Aurora**    | SQL      | Managed + tối ưu | Hiệu năng cao, scale tốt       | Ứng dụng lớn, high-traffic           |
| **DynamoDB**  | NoSQL    | Serverless    | Nhanh, không cần quản lý server | Mobile, real-time, logs              |
| **DocumentDB**| NoSQL    | Managed       | MongoDB-compatible              | App dùng JSON document               |
| **ElastiCache**| Cache   | In-memory     | Redis/Memcached, tăng tốc       | Cache database, pub/sub              |

###  🚀 Di chuyển (Migrate) dữ liệu từ local lên AWS

#### AWS Database Migration Service (DMS):
- Dịch vụ giúp **di chuyển database** từ local/on-premise lên AWS.
- Hỗ trợ real-time replication hoặc migrate 1 lần.
- Hỗ trợ MySQL, PostgreSQL, SQL Server, Oracle, MongoDB...

**Các bước:**
1. Tạo **source endpoint** (local DB).
2. Tạo **target endpoint** (RDS, Aurora, DynamoDB...).
3. Tạo migration task → Chạy.

👉 Phù hợp với database **lớn** hoặc yêu cầu **downtime thấp**.

#### Backup và Restore thủ công

MySQL / PostgreSQL:
```bash
# Backup:
mysqldump -u root -p mydb > backup.sql

# Restore vào RDS/Aurora:
mysql -h <rds-endpoint> -u admin -p mydb < backup.sql
```

MongoDB (DocumentDB):
```bash
# Backup:
mongodump --db=mydb --out=/backup/

# Restore vào DocumentDB:
mongorestore --host <documentdb-endpoint> /backup/mydb
```

DynamoDB: Export dữ liệu thành JSON rồi dùng AWS CLI:
```bash
aws dynamodb batch-write-item --request-items file://data.json
```

S3:
```bash
aws s3 cp ./local-folder s3://my-bucket/ --recursive
```

## 8. High Availability (Elastic Load Balancing/Auto Scaling Group)

Để đảm bảo **ứng dụng luôn sẵn sàng** (HA - High Availability), AWS cung cấp các thành phần như:

- **Elastic Load Balancer (ELB)** – cân bằng tải.
- **Auto Scaling Group (ASG)** – tự động scale số lượng EC2.
- **Multi-AZ** – phân phối workload qua nhiều Availability Zone.

### 8.1. 🔄 Elastic Load Balancer (ELB)

- Là dịch vụ giúp **phân phối traffic đều** đến nhiều EC2 instance ở các AZ khác nhau.
- Hoạt động ở layer 4 (TCP) hoặc layer 7 (HTTP/HTTPS).

Các loại ELB:
| Loại ELB         | Mô tả ngắn gọn                              |
|------------------|---------------------------------------------|
| **ALB** (App Load Balancer) | Dùng cho HTTP/HTTPS, route theo path, host |
| **NLB** (Network Load Balancer) | Dùng cho TCP/UDP, hiệu năng cao, low latency |
| **CLB** (Classic - cũ)     | Hỗn hợp, không còn khuyến khích dùng |

Tính năng:
- Tự động health check EC2.
- Tự loại bỏ instance unhealthy.
- Dễ dàng tích hợp với SSL/TLS.

---

### 8.2. 📈 Auto Scaling Group (ASG)

- ASG giúp **tự động thêm hoặc giảm số lượng EC2 instance** theo nhu cầu (dựa trên CPU, Network...).

Tính năng:
- Định nghĩa số lượng **min / max / desired** instance.
- Tự động thay thế EC2 khi bị crash (health check failed).
- Phân phối instance ra **nhiều AZ**.

Chính sách scaling:
- **Dynamic Scaling**: scale lên/xuống theo chỉ số metric (CPU > 80%, v.v).
- **Scheduled Scaling**: scale vào thời điểm cụ thể.
- **Predictive Scaling**: sử dụng AI để dự đoán nhu cầu trong tương lai (chỉ dùng được trong một số region).

---

### 8.3. 🧱 Kiến trúc triển khai HA cơ bản

```text
Client
  │
[ Route 53 ]
  │
[ ELB ]
  ├────► EC2 instance 1 (AZ-1)
  ├────► EC2 instance 2 (AZ-2)
  └────► EC2 instance 3 (AZ-3)
        ↑ managed by Auto Scaling Group
```

📚 *Tài liệu này giúp tổng hợp kiến thức căn bản khi học và làm việc với AWS. Có thể dùng cho mục đích ôn tập, giảng dạy hoặc xây dựng nền tảng cho dự án thực tế.*
