# Trần Lê Phương Khánh -22647691-NHQENSV003209099E73400
# E-Project: Hệ thống Microservices Bán hàng

Đây là một dự án backend mô phỏng một hệ thống thương mại điện tử đơn giản, được xây dựng theo kiến trúc microservices.

## Kiến trúc hệ thống

Hệ thống bao gồm các thành phần chính sau:
* **API Gateway**: Cổng chính tiếp nhận tất cả request từ client và điều hướng đến các service phù hợp.
* **Auth Service**: Xử lý tất cả các vấn đề liên quan đến xác thực người dùng (đăng ký, đăng nhập, JWT).
* **Product Service**: Quản lý thông tin về sản phẩm.
* **Order Service**: Xử lý logic đặt hàng và tương tác với các service khác qua message queue.
* **MongoDB**: Cơ sở dữ liệu NoSQL để lưu trữ dữ liệu cho các service.
* **RabbitMQ**: Message Broker để giao tiếp bất đồng bộ giữa các service (ví dụ: khi một đơn hàng được tạo).


---
## 🧠 Công nghệ sử dụng
- Node.js & Express.js  
- Docker & Docker Compose  
- JWT (xác thực người dùng)  
- MongoDB / Mongoose  
- RabbitMQ (nếu có cấu hình)

## Yêu cầu cài đặt

Để chạy dự án này, bạn cần cài đặt các phần mềm sau trên máy tính của mình:
* [Docker](https://www.docker.com/products/docker-desktop/) và Docker Compose
* [Postman](https://www.postman.com/downloads/) (để test API)
* [Git](https://git-scm.com/downloads/)

---

## Cài đặt và Chạy dự án với Docker

Đây là cách đơn giản và được khuyến khích để chạy toàn bộ hệ thống chỉ với một vài lệnh.

### Bước 1: Tải mã nguồn

```bash
Clone dự án về
```

### Bước 2: Tạo các file cấu hình Docker

Bạn cần tạo các file sau trong dự án của mình.

#### a. Tạo `Dockerfile` cho mỗi service

Tạo một file tên là `Dockerfile` trong **mỗi thư mục service** (`auth`, `product`, `order`, `api-gateway`) với nội dung giống hệt nhau:

```dockerfile
# Sử dụng một image Node.js chính thức làm nền
FROM node:18-alpine

# Tạo thư mục làm việc bên trong container
WORKDIR /app

# Sao chép package.json và package-lock.json
COPY package*.json ./

# Cài đặt các dependencies
RUN npm install

# Sao chép toàn bộ mã nguồn của service vào
COPY . .

# Mở cổng mà ứng dụng sẽ chạy
EXPOSE 3000

# Lệnh để khởi chạy ứng dụng
CMD ["npm", "start"]
```

#### b. Tạo file `docker-compose.yml`

Ở thư mục **gốc** của dự án, tạo một file tên là `docker-compose.yml` với nội dung sau:

```yaml
services:
  # Service MongoDB
  mongo:
    image: mongo
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

  # Service RabbitMQ
  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - "5672:5672"
      - "15672:15672"

  # Service Auth
  auth:
    build: ./auth
    ports:
      - "3001:3000"
    environment:
      - MONGODB_AUTH_URI=mongodb://mongo:27017/auth_db
      - JWT_SECRET=daylamotkhoabimatratmanhcuaban
    depends_on:
      - mongo

  # Service Product
  product:
    build: ./product
    ports:
      - "3002:3000"
    environment:
      - MONGODB_PRODUCT_URI=mongodb://mongo:27017/product_db
    depends_on:
      - mongo
      - rabbitmq

  # Service Order
  order:
    build: ./order
    ports:
      - "3003:3000"
    environment:
      - MONGODB_ORDER_URI=mongodb://mongo:27017/order_db
    depends_on:
      - mongo
      - rabbitmq

  # API Gateway
  api-gateway:
    build: ./api-gateway
    ports:
      - "3000:3000"
    depends_on:
      - auth
      - product
      - order

volumes:
  mongo-data:
```

### Bước 3: Khởi chạy hệ thống

Mở terminal ở thư mục gốc của dự án và chạy lệnh:
```bash
docker-compose up --build
```
Lệnh này sẽ build image cho từng service và khởi chạy toàn bộ hệ thống. Bạn sẽ thấy log của tất cả các service hiển thị trên màn hình.

Để dừng hệ thống, nhấn `Ctrl + C`.

---

## Kiểm tra (Testing) với Postman

Sau khi hệ thống đã chạy, bạn có thể dùng Postman để kiểm tra các API. Mọi request đều được gửi đến API Gateway tại `http://localhost:3000`.

### 1. Đăng ký tài khoản
* **Method**: `POST`
* **URL**: `http://localhost:3000/register`
* **Body** (`raw`, `JSON`):
    ```json
    {
        "username": "testuser1",
        "password": "password123"
    }
    Example:
    ```

### 2. Đăng nhập
* **Method**: `POST`
* **URL**: `http://localhost:3000/login`
* **Body** (`raw`, `JSON`):
    ```json
    {
        "username": "testuser1",
        "password": "password123"
    }
    Ecample:
    ```


* **Kết quả**: Copy lại giá trị `token` từ response để sử dụng cho các request tiếp theo.

### 3. Tạo sản phẩm mới
* **Method**: `POST`
* **URL**: `http://localhost:3000/products/api/products`
* **Authorization**: Chọn `Bearer Token` và dán `token` đã copy ở bước 2.
* **Body** (`raw`, `JSON`):
    ```json
    {
        "name": "Laptop Siêu Mỏng 2025",
        "description": "Đây là mô tả chi tiết cho chiếc laptop siêu mỏng đời mới.",
        "price": 25990000
    }
    ```
   
* **Kết quả**: Copy lại giá trị `_id` của sản phẩm vừa tạo.

### 4. Tạo đơn hàng mới
* **Method**: `POST`
* **URL**: `http://localhost:3000/orders/create` (Lưu ý: đường dẫn này có thể khác tùy theo code của bạn)
* **Authorization**: Tiếp tục dùng `Bearer Token`.
* **Body** (`raw`, `JSON`):
    ```json
    {
        "products": [
            {
                "ids": "DÁN_ID_SẢN_PHẨM_VỪA_TẠO_Ở_TRÊN_VÀO_ĐÂY"

            }
        ]
    }
    ```
---
