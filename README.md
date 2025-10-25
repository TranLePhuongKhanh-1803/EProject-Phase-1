# Trần Lê Phương Khánh -22647691-Mã máy:NHQENSV003209099E73400 
# 🧩 EProject Phase 1 — Microservices E-Commerce System
## 📘 Giới thiệu
Dự án **EProject-Phase-1** là một hệ thống thương mại điện tử mô phỏng theo mô hình **Microservices Architecture**.  
Mỗi module hoạt động độc lập dưới dạng container Docker, giao tiếp với nhau thông qua API Gateway.  

### Các service chính:
- **API Gateway** – trung gian điều phối request đến từng dịch vụ.  
- **Auth Service** – xử lý đăng ký, đăng nhập, xác thực người dùng (JWT).  
- **Product Service** – quản lý sản phẩm (CRUD).  
- **Order Service** – quản lý đơn hàng, xác minh người dùng qua token.  

---


## 🚀 Cách chạy dự án

### 1. Cài đặt Docker & Docker Compose  
- [Tải Docker Desktop](https://www.docker.com/products/docker-desktop)  
- [Hướng dẫn cài đặt Docker Compose](https://docs.docker.com/compose/install/)

---

## 🧠 Công nghệ sử dụng
- Node.js & Express.js  
- Docker & Docker Compose  
- JWT (xác thực người dùng)  
- MongoDB / Mongoose  
- RabbitMQ (nếu có cấu hình)

