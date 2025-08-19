# HMall - Microservices E-commerce Platform

[![Java](https://img.shields.io/badge/Java-11-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-2.7.12-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-2021.0.3-blue.svg)](https://spring.io/projects/spring-cloud)
[![Maven](https://img.shields.io/badge/Maven-3.6+-red.svg)](https://maven.apache.org/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## 📋 Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Services](#services)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [API Documentation](#api-documentation)
- [Database Schema](#database-schema)
- [Security](#security)
- [Monitoring](#monitoring)
- [Deployment](#deployment)
- [Contributing](#contributing)
- [License](#license)

## 🌟 Overview

HMall is a comprehensive microservices-based e-commerce platform built with Spring Cloud ecosystem. It demonstrates modern enterprise-grade application development patterns including service discovery, configuration management, load balancing, circuit breakers, distributed transactions, and message queuing.

The platform provides a complete online shopping experience with features like user management, product catalog, shopping cart, order processing, payment system, and real-time inventory management.

## 🏗️ Architecture

### Microservices Architecture
The application follows a microservices architecture pattern with the following components:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend/UI   │────│   API Gateway   │────│  Load Balancer  │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                │
                ┌───────────────┼───────────────┐
                │               │               │
        ┌───────▼──────┐ ┌──────▼──────┐ ┌─────▼────────┐
        │ User Service │ │Item Service │ │Trade Service │
        └──────────────┘ └─────────────┘ └──────────────┘
                │               │               │
        ┌───────▼──────┐ ┌──────▼──────┐ ┌─────▼────────┐
        │ Cart Service │ │ Pay Service │ │Common Module │
        └──────────────┘ └─────────────┘ └──────────────┘
                              │
                    ┌─────────▼─────────┐
                    │   Shared Database │
                    └───────────────────┘
```

### Service Communication
- **Synchronous Communication**: OpenFeign for REST API calls between services
- **Asynchronous Communication**: RabbitMQ for event-driven messaging
- **Service Discovery**: Nacos for service registration and discovery
- **Configuration Management**: Nacos Config for centralized configuration
- **API Gateway**: Spring Cloud Gateway for request routing and filtering

## 🛠️ Technology Stack

### Core Technologies
- **Java 11** - Programming language
- **Spring Boot 2.7.12** - Application framework
- **Spring Cloud 2021.0.3** - Microservices framework
- **Spring Cloud Alibaba 2021.0.4.0** - Alibaba's Spring Cloud extensions

### Data & Persistence
- **MySQL 8.0.23** - Primary database
- **MyBatis Plus 3.4.2** - ORM framework
- **Redis** - Caching and session storage
- **HikariCP** - Connection pooling

### Microservices Infrastructure
- **Nacos** - Service discovery and configuration management
- **Spring Cloud Gateway** - API Gateway
- **OpenFeign** - Declarative REST client
- **Spring Cloud LoadBalancer** - Client-side load balancing
- **Sentinel** - Circuit breaker and traffic control
- **Seata** - Distributed transaction management

### Message Queue
- **RabbitMQ** - Asynchronous messaging
- **Spring AMQP** - Message queue integration

### Security & Authentication
- **JWT (JSON Web Tokens)** - Stateless authentication
- **RSA-256** - Token signing algorithm
- **Spring Security** - Security framework

### Documentation & Monitoring
- **Knife4j/Swagger** - API documentation
- **Spring Boot Actuator** - Application monitoring
- **Logback** - Logging framework

### Build & DevOps
- **Maven** - Build automation
- **Docker** - Containerization
- **Lombok** - Code generation
- **Hutool** - Java utility library

## 📁 Project Structure

```
hmall/
├── hm-api/                    # API interfaces and DTOs
│   ├── src/main/java/com/hmall/api/
│   │   ├── client/           # Feign clients for service communication
│   │   ├── dto/              # Data Transfer Objects
│   │   └── fallback/         # Fallback implementations
│   └── pom.xml
├── hm-common/                 # Shared utilities and configurations
│   ├── src/main/java/com/hmall/common/
│   │   ├── config/           # Common configurations
│   │   ├── exception/        # Custom exceptions
│   │   ├── interceptor/      # Common interceptors
│   │   ├── utils/            # Utility classes
│   │   └── domain/           # Common domain objects
│   └── pom.xml
├── hm-gateway/                # API Gateway service
│   ├── src/main/java/com/hmall/gateway/
│   │   ├── filters/          # Gateway filters
│   │   ├── config/           # Gateway configuration
│   │   └── properties/       # Configuration properties
│   └── pom.xml
├── hm-service/                # Monolithic service (legacy)
│   ├── src/main/java/com/hmall/
│   │   ├── controller/       # REST controllers
│   │   ├── service/          # Business logic
│   │   ├── domain/           # Entity classes
│   │   ├── mapper/           # MyBatis mappers
│   │   └── config/           # Configuration classes
│   ├── src/main/resources/
│   │   ├── mapper/           # MyBatis XML mappings
│   │   └── application*.yaml # Configuration files
│   ├── Dockerfile
│   └── pom.xml
├── item-service/              # Product management service
├── cart-service/              # Shopping cart service
├── user-service/              # User management service
├── trade-service/             # Order and trading service
├── pay-service/               # Payment processing service
├── logs/                      # Application logs
└── pom.xml                    # Parent POM
```

## 🔧 Services

### 1. **API Gateway (hm-gateway)** - Port 8080
- **Purpose**: Single entry point for all client requests
- **Features**:
  - Request routing and load balancing
  - JWT authentication and authorization
  - Rate limiting and request filtering
  - Cross-cutting concerns handling

### 2. **Item Service (item-service)** - Port 8081
- **Purpose**: Product catalog and inventory management
- **Features**:
  - Product CRUD operations
  - Inventory tracking and stock management
  - Product search and filtering
  - Category and brand management
- **Database**: `hm-item`

### 3. **Cart Service (cart-service)** - Port 8082
- **Purpose**: Shopping cart functionality
- **Features**:
  - Add/remove items from cart
  - Cart persistence and synchronization
  - Bulk operations on cart items
  - Cart validation before checkout
- **Database**: `hm-cart`

### 4. **User Service (user-service)** - Port 8083
- **Purpose**: User account and profile management
- **Features**:
  - User registration and authentication
  - Profile management
  - Address book management
  - Balance and wallet operations
- **Database**: `hm-user`

### 5. **Trade Service (trade-service)** - Port 8084
- **Purpose**: Order processing and management
- **Features**:
  - Order creation and lifecycle management
  - Order status tracking
  - Integration with payment and inventory services
  - Distributed transaction handling
- **Database**: `hm-trade`

### 6. **Pay Service (pay-service)** - Port 8085
- **Purpose**: Payment processing
- **Features**:
  - Payment order creation
  - Multiple payment methods support
  - Payment status tracking
  - Transaction history
- **Database**: `hm-pay`

### 7. **Legacy Service (hm-service)** - Port 8080
- **Purpose**: Monolithic implementation (for comparison/migration)
- **Features**: Combined functionality of all microservices

## ✨ Features

### User Management
- User registration and login with JWT authentication
- User profile and address management
- Account balance and transaction history
- Password encryption and security

### Product Catalog
- Comprehensive product information management
- Category and brand organization
- Advanced search and filtering capabilities
- Real-time inventory tracking
- Product image and specification management

### Shopping Experience
- Persistent shopping cart across sessions
- Cart item management (add, update, remove)
- Bulk operations on cart items
- Cart validation and stock checking

### Order Processing
- Seamless order creation workflow
- Multiple payment method support (currently balance payment)
- Order status tracking and lifecycle management
- Distributed transaction support with Seata
- Automatic inventory deduction

### Payment System
- Secure payment processing
- Payment order generation and tracking
- Balance-based payment implementation
- Transaction logging and audit trail

### System Features
- JWT-based stateless authentication
- Service-to-service communication via OpenFeign
- Centralized configuration management
- Circuit breaker pattern implementation
- Distributed transaction support
- Event-driven architecture with RabbitMQ
- Comprehensive API documentation

## ⚡ Prerequisites

- **Java 11** or higher
- **Maven 3.6+**
- **MySQL 8.0+**
- **Redis 6.0+**
- **RabbitMQ 3.8+**
- **Nacos 2.0+** (for service discovery and configuration)
- **Docker** (optional, for containerized deployment)

## 🚀 Installation

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/hmall.git
cd hmall
```

### 2. Database Setup
Create the required databases in MySQL:
```sql
CREATE DATABASE hmall CHARACTER SET utf8mb4;
CREATE DATABASE hm_user CHARACTER SET utf8mb4;
CREATE DATABASE hm_item CHARACTER SET utf8mb4;
CREATE DATABASE hm_cart CHARACTER SET utf8mb4;
CREATE DATABASE hm_trade CHARACTER SET utf8mb4;
CREATE DATABASE hm_pay CHARACTER SET utf8mb4;
```

### 3. Infrastructure Setup

#### Start Nacos
```bash
# Download and start Nacos
wget https://github.com/alibaba/nacos/releases/download/2.0.3/nacos-server-2.0.3.tar.gz
tar -xzf nacos-server-2.0.3.tar.gz
cd nacos/bin
./startup.sh -m standalone
```

#### Start Redis
```bash
redis-server
```

#### Start RabbitMQ
```bash
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```

### 4. Build the Project
```bash
mvn clean install
```

### 5. Start Services

#### Option A: Start Individual Services
```bash
# Start API Gateway
cd hm-gateway && mvn spring-boot:run

# Start Item Service
cd item-service && mvn spring-boot:run

# Start User Service  
cd user-service && mvn spring-boot:run

# Start Cart Service
cd cart-service && mvn spring-boot:run

# Start Trade Service
cd trade-service && mvn spring-boot:run

# Start Pay Service
cd pay-service && mvn spring-boot:run
```

#### Option B: Docker Deployment
```bash
# Build Docker images
docker build -t hmall-gateway ./hm-gateway
docker build -t hmall-item ./item-service
# ... (build other services)

# Use Docker Compose (if docker-compose.yml is available)
docker-compose up -d
```

## ⚙️ Configuration

### Environment Variables
Set the following environment variables or update the configuration files:

```yaml
# Database Configuration
hm:
  db:
    host: localhost  # MySQL host
    pw: your_password  # MySQL password

# Nacos Configuration  
spring:
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
      config:
        server-addr: localhost:8848

# Redis Configuration
spring:
  redis:
    host: localhost
    port: 6379
    password: your_redis_password
```

### JWT Configuration
The JWT configuration is located in each service's `application.yaml`:
```yaml
hm:
  jwt:
    location: classpath:hmall.jks
    alias: hmall
    password: hmall123
    tokenTTL: 30m
```

### Service Ports
- API Gateway: `8080`
- Item Service: `8081`
- Cart Service: `8082`
- User Service: `8083`
- Trade Service: `8084`
- Pay Service: `8085`

## 📚 API Documentation

Once the services are running, you can access the API documentation:

- **API Gateway**: http://localhost:8080/doc.html
- **Item Service**: http://localhost:8081/doc.html
- **User Service**: http://localhost:8083/doc.html
- **Cart Service**: http://localhost:8082/doc.html
- **Trade Service**: http://localhost:8084/doc.html
- **Pay Service**: http://localhost:8085/doc.html

The documentation is powered by Knife4j (enhanced Swagger UI) and provides:
- Interactive API testing
- Request/response examples
- Parameter descriptions
- Authentication requirements

### Key API Endpoints

#### User Service
- `POST /users/login` - User authentication
- `PUT /users/money/deduct` - Deduct user balance

#### Item Service  
- `GET /items` - List products with pagination
- `GET /items/{id}` - Get product details
- `PUT /items/stock/deduct` - Deduct product stock

#### Cart Service
- `GET /carts` - Get user's cart items
- `POST /carts` - Add item to cart
- `DELETE /carts` - Remove items from cart

#### Trade Service
- `POST /orders` - Create new order
- `GET /orders/{id}` - Get order details
- `PUT /orders/{id}` - Mark order as paid

#### Pay Service
- `POST /pay-orders` - Create payment order
- `POST /pay-orders/{id}` - Process payment

## 🗄️ Database Schema

### Main Entities

#### User Table
```sql
CREATE TABLE `user` (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `username` varchar(255) NOT NULL COMMENT 'Username',
  `password` varchar(255) NOT NULL COMMENT 'Encrypted password',
  `phone` varchar(20) DEFAULT NULL COMMENT 'Phone number',
  `status` tinyint DEFAULT '1' COMMENT 'Status (1=active, 2=frozen)',
  `balance` int DEFAULT '0' COMMENT 'Account balance in cents',
  `create_time` timestamp DEFAULT CURRENT_TIMESTAMP,
  `update_time` timestamp DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`)
);
```

#### Item Table
```sql
CREATE TABLE `item` (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL COMMENT 'Product name',
  `price` int NOT NULL COMMENT 'Price in cents',
  `stock` int NOT NULL DEFAULT '0' COMMENT 'Stock quantity',
  `image` varchar(500) DEFAULT NULL COMMENT 'Product image URL',
  `category` varchar(100) DEFAULT NULL COMMENT 'Category',
  `brand` varchar(100) DEFAULT NULL COMMENT 'Brand',
  `spec` text COMMENT 'Specifications',
  `sold` int DEFAULT '0' COMMENT 'Sales count',
  `comment_count` int DEFAULT '0' COMMENT 'Review count',
  `isAD` bit(1) DEFAULT b'0' COMMENT 'Is advertisement',
  `status` tinyint DEFAULT '1' COMMENT 'Status (1=active, 2=offline, 3=deleted)',
  `create_time` timestamp DEFAULT CURRENT_TIMESTAMP,
  `update_time` timestamp DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
);
```

#### Order Table
```sql
CREATE TABLE `order` (
  `id` bigint NOT NULL,
  `total_fee` int NOT NULL COMMENT 'Total amount in cents',
  `payment_type` tinyint DEFAULT NULL COMMENT 'Payment type (1=Alipay, 2=WeChat, 3=Balance)',
  `user_id` bigint NOT NULL COMMENT 'User ID',
  `status` tinyint DEFAULT '1' COMMENT 'Order status',
  `create_time` timestamp DEFAULT CURRENT_TIMESTAMP,
  `pay_time` timestamp NULL DEFAULT NULL,
  `consign_time` timestamp NULL DEFAULT NULL,
  `end_time` timestamp NULL DEFAULT NULL,
  `close_time` timestamp NULL DEFAULT NULL,
  `comment_time` timestamp NULL DEFAULT NULL,
  `update_time` timestamp DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
);
```

## 🔐 Security

### Authentication & Authorization
- **JWT-based Authentication**: Stateless token-based authentication system
- **RSA-256 Signing**: Tokens signed with RSA-256 algorithm for enhanced security
- **Token Expiration**: Configurable token TTL (default: 30 minutes)
- **Gateway-level Authorization**: Centralized authentication at the API Gateway

### Security Features
- Password encryption using BCrypt
- Request/response validation
- SQL injection prevention through MyBatis
- CORS configuration for cross-origin requests
- Rate limiting and request throttling

### Excluded Paths
The following paths are excluded from authentication:
- `/search/**` - Product search endpoints
- `/users/login` - User login endpoint  
- `/items/**` - Public product information
- `/hi` - Health check endpoint

## 📊 Monitoring

### Application Monitoring
- **Spring Boot Actuator**: Built-in monitoring and management endpoints
- **Custom Health Checks**: Service-specific health indicators
- **Metrics Collection**: JVM and application metrics
- **Log Aggregation**: Centralized logging with configurable levels

### Service Monitoring
- **Service Discovery**: Real-time service registry via Nacos
- **Circuit Breaker**: Sentinel for fault tolerance and traffic control
- **Load Balancing**: Client-side load balancing with health checks

### Available Endpoints
- `/actuator/health` - Service health status
- `/actuator/metrics` - Application metrics
- `/actuator/info` - Application information

## 🚢 Deployment

### Docker Deployment

#### Build Images
```bash
# Build service images
docker build -t hmall/gateway:latest ./hm-gateway
docker build -t hmall/item-service:latest ./item-service
docker build -t hmall/user-service:latest ./user-service
docker build -t hmall/cart-service:latest ./cart-service
docker build -t hmall/trade-service:latest ./trade-service
docker build -t hmall/pay-service:latest ./pay-service
```

#### Run Containers
```bash
# Run infrastructure services
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:8.0
docker run -d --name redis -p 6379:6379 redis:latest
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
docker run -d --name nacos -p 8848:8848 nacos/nacos-server:latest

# Run application services
docker run -d --name hmall-gateway -p 8080:8080 hmall/gateway:latest
docker run -d --name hmall-item -p 8081:8081 hmall/item-service:latest
# ... (run other services)
```

### Production Considerations
- Use environment-specific configuration files
- Implement proper secret management
- Set up monitoring and alerting
- Configure proper logging levels
- Implement backup and disaster recovery
- Use container orchestration (Kubernetes) for scalability

## 🤝 Contributing

We welcome contributions to the HMall project! Please follow these guidelines:

### Development Workflow
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Code Standards
- Follow Java coding conventions
- Write comprehensive unit tests
- Update documentation for new features
- Ensure all tests pass before submitting
- Use meaningful commit messages

### Issue Reporting
- Use the issue tracker for bug reports and feature requests
- Provide detailed reproduction steps for bugs
- Include system information and error logs

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Spring Boot](https://spring.io/projects/spring-boot) - Application framework
- [Spring Cloud](https://spring.io/projects/spring-cloud) - Microservices framework
- [Spring Cloud Alibaba](https://github.com/alibaba/spring-cloud-alibaba) - Alibaba's Spring Cloud extensions
- [MyBatis Plus](https://baomidou.com/) - ORM framework
- [Nacos](https://nacos.io/) - Service discovery and configuration management
- [Hutool](https://hutool.cn/) - Java utility library

---

## 📞 Support

For support and questions:
- 📧 Email: zhanghuyi@itcast.cn
- 🐛 Issues: [GitHub Issues](https://github.com/your-username/hmall/issues)
- 📚 Documentation: [Project Wiki](https://github.com/your-username/hmall/wiki)

---

**Happy Coding! 🎉**
