# EchoMart E-commerce

## Overview
EchoMart is a demonstration project designed to showcase expertise in asynchronous messaging, Docker containerization, and microservices architecture with a Clean Architecture approach. It simulates a basic e-commerce system with three independent microservices: OrderService, PaymentService, and SupplyService, communicating through RabbitMQ queues using background workers for message consumption. Note: This project intentionally omits a database to focus on messaging, Docker, and microservices concepts, simulating operations in memory.

The goal is to illustrate how microservices can be decoupled, scalable, and maintainable, leveraging Clean Architecture and robust messaging with RabbitMQ.

## Project Structure
```
EchoMart
  ├── SupplyService
  │   ├── SupplyService.sln
  │   ├── SupplyService.csproj           (Presentation - API)
  │   ├── SupplyService.Domain.csproj    (Domain - Entities)
  │   ├── SupplyService.Application.csproj (Application - Business Logic)
  │   ├── SupplyService.Infrastructure.csproj (Infrastructure - Messaging)
  │   ├── Dockerfile
  │   └── (other files per layer)
  ├── OrderService
  │   ├── OrderService.sln
  │   ├── OrderService.csproj            (Presentation - API)
  │   ├── OrderService.Domain.csproj     (Domain - Entities)
  │   ├── OrderService.Application.csproj (Application - Business Logic)
  │   ├── OrderService.Infrastructure.csproj (Infrastructure - Messaging)
  │   ├── Dockerfile
  │   └── (other files per layer)
  ├── PaymentService
  │   ├── PaymentService.sln
  │   ├── PaymentService.csproj          (Presentation - API)
  │   ├── PaymentService.Domain.csproj   (Domain - Entities)
  │   ├── PaymentService.Application.csproj (Application - Business Logic)
  │   ├── PaymentService.Infrastructure.csproj (Infrastructure - Messaging)
  │   ├── Dockerfile
  │   └── (other files per layer)
  ├── docker-compose.yml
```
- SupplyService: Manages inventory updates, consuming messages from the payment-to-supply queue via a background worker.
- OrderService: Handles order creation and publishes messages to the order-to-payment queue.
- PaymentService: Simulates payment processing (QR Code generation) and publishes messages to the payment-to-supply queue, consuming from order-to-payment via a background worker.

## Architecture
The project follows a Clean Architecture pattern within a microservices framework:

### Microservices with Clean Architecture
- Each microservice is standalone, structured in four layers:
  - Domain: Core entities and business rules (e.g., Order, Stock).
  - Application: Business logic and use cases (e.g., IOrderService, StockService).
  - Infrastructure: External systems integration (e.g., RabbitMQ messaging with workers).
  - Presentation: API endpoints (e.g., controllers).
- Built using ASP.NET Core 8 and deployed in Docker containers.

### Messaging
- RabbitMQ is used for asynchronous communication with durable queues.
- Queues:
  - order-to-payment: Transmits orders from OrderService to PaymentService.
  - payment-to-supply: Transmits payment confirmations from PaymentService to SupplyService.
- Background workers (using BackgroundService) handle message consumption in SupplyService and PaymentService.

### Docker
- Each microservice and RabbitMQ run in separate containers.
- The docker-compose.yml orchestrates services with a custom network (echomart-network).

## System Workflow
1. Order Creation:
   - A client submits an order via POST /api/order to the OrderService.
   - The OrderService generates a unique OrderId and publishes a message to the order-to-payment queue.
   - Example message: {"OrderId": "guid", "ProductId": 1, "Quantity": 10}.

2. Payment Processing:
   - A background worker in PaymentService consumes the message from the order-to-payment queue.
   - It simulates QR Code generation (logged to console).
   - The client confirms payment via POST /api/payment/confirm.
   - The PaymentService publishes a message to the payment-to-supply queue.
   - Example message: {"OrderId": "guid", "Status": "Paid"}.

3. Inventory Update:
   - A background worker in SupplyService consumes the message from the payment-to-supply queue.
   - It simulates an inventory update (logged to console).

## Prerequisites
- .NET SDK 8.0
- Docker and Docker Compose
- Code editor (e.g., Visual Studio)

## How to Run
1. Clone the repository or create the files as per the step-by-step guide.
2. In the EchoMart root directory, run:
   docker-compose up --build
3. Test the workflow:
   - Create an order:
     curl -X POST http://localhost:5002/api/order -H "Content-Type: application/json" -d '{"ProductId": 1, "Quantity": 10}'
   - Confirm payment (replace <OrderId> with the returned value):
     curl -X POST http://localhost:5003/api/payment/confirm -H "Content-Type: application/json" -d '{"OrderId": "<OrderId>"}'
4. View logs:
   docker-compose logs
5. Monitor queues in RabbitMQ: http://localhost:15672 (username: guest, password: guest).

## Technologies Used
- .NET 8: Framework for building microservices with Clean Architecture and background workers.
- RabbitMQ: Asynchronous messaging system with durable queues.
- Docker: Containerization platform.
- Docker Compose: Orchestration tool for multi-container applications.

## Purpose
This project demonstrates:
- Clean Architecture: Layered design for maintainability and testability within microservices.
- Messaging: Asynchronous communication using RabbitMQ queues with background workers.
- Docker: Containerization and orchestration for scalable deployment.
- Microservices: Decoupled, modular architecture.

Ideal for portfolios or technical interviews focusing on modern software engineering practices!
