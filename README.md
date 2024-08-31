
# Orbital test project

This project contains two microservices: `accounts-service` and `reporting-service`. Both services are built using the Serverless Framework, TypeScript, and Mongoose.

## Getting Started

Follow these steps to set up and deploy the project locally.

### Prerequisites

Make sure you have the following installed:

- Node.js (version 14.x or later)
- Serverless Framework (version 3.x or later)
- AWS CLI (configured with your AWS credentials)

### 1. Copy Environment Variables

First, copy the environment variables from the example file to a `.env` file:

```bash
cp .env.example .env
```

### 2. Install Node.js Packages

Navigate to each service directory and install the necessary Node.js packages:

```bash
cd accounts-service
npm install

cd ../reporting-service
npm install
```

### 3. Deploying the Services

You need to deploy each service separately using the Serverless Framework:

#### Deploy `accounts-service`:

```bash
cd accounts-service
serverless deploy
```

#### Deploy `reporting-service`:

```bash
cd ../reporting-service
serverless deploy
```

This will deploy both services to your specified AWS environment.

### 4. Local Development

To work with the services locally, use the following command to run a local server:

```bash
serverless offline
```

This will start the services on your local machine, allowing you to test and develop without deploying to AWS.

### 5. Testing

You can run tests using:

```bash
npm run test
```

This will execute the test suite for the services.

## Note
> The project supports two stages - `dev` and `production`. Both have CI/CD pipelines setup for branches  `main` and `production`, respectively.

## Additional Information

- **Accounts Service**: Manages bank accounts, balances, and transactions, and publishes events to AWS EventBridge.
- **Reporting Service**: Listens to transaction events and generates monthly statements.
- **AWS Eventbridge**: Relays events published by `accounts-service` to be listened by other services. In this case only `createTransaction` event 
  is listened by `reporting-service`


# API Documentation

Currently the services are deployed on AWS lambda with the following URLs
1. **Accounts service**: `https://2sm6p2g9o0.execute-api.eu-north-1.amazonaws.com`
2. **Reporting service**: `https://wvse1h39pa.execute-api.eu-north-1.amazonaws.com`

## Accounts Service
> Note: Currencies can only be added from the list of supported currencies in accounts-services/constants/currencies.ts

1. **Create Account API**  
   **Endpoint:** `POST /dev/accounts`  
   **Request Body:**
   ```json
   {
       "customerId": <String>,
       "accountNumber": <String>,
       "currencies": ["USD", "GBP", "BTC"]
   }
   ```

2. **Get Account**  
   **Endpoint:** `GET /dev/accounts/{accountId}`  
   Example: `GET /dev/accounts/66d27855bb446a4240fee583`

3. **Update Account**  
   **Endpoint:** `PUT /dev/accounts/{accountId}`  
   Example: `PUT /dev/accounts/66d1705c1883ebd358839d3c`  
   **Request Body:**
   ```json
   {
       "currencies": ["GBP", "USD", "CNY"],
       "customerId": <String>,
       "accountNumber": <String>
   }
   ```

4. **Create Transaction**  
   **Endpoint:** `POST /dev/transactions`  
   **Request Body:**
   ```json
   {
       "accountId": "66d27855bb446a4240fee583",
       "type": "INBOUND" | "OUTBOUND",
       "currency": "GBP",
       "amount": <number>
   }
   ```

5. **Get Transactions**  
   **Endpoint:** `GET /dev/transactions/{transactionId}`  
   Example: `GET /dev/transactions/66d21c4913c5b942cdb00451`

## Reporting Service

1. **Get monthly statements**  
   **Endpoint:** `POST /dev/statements/{accountId}/{year}/{monthy}/`
   Example: `GET /dev/transactions/66d21c4913c5b942cdb00451/2024/08`


# Architectural Decisions

## Technical Decisions

1. **Using TypeScript**  
   TypeScript is used for its strong typing capabilities, which enhances code quality, reduces runtime errors, and improves overall maintainability by providing robust tooling and autocomplete features.

2. **Using Class Validators for API Request Validation and DTOs**  
   Class Validators were used to ensure that all incoming API requests are validated against predefined rules, making our data transfer objects (DTOs) reliable and safeguarding against invalid data inputs.

3. **Using Two Stages: Development and Production**  
   The project is structured to support both development and production environments. This allows for a clear separation between testing and live environments, ensuring that changes can be tested thoroughly before being deployed to production.

## Product Decisions

1. **Balance Management in Accounts**  
   The architecture enforces that the `balances` field in accounts cannot be directly updated or created. Instead, balances are modified exclusively through the creation of transactions, ensuring that all changes to account balances are traceable and follow the correct business logic.

2. **Currency Support Enforcement**  
   The supported currencies within the framework are enforced via a global variable in the `accounts-service`. To use a currency in `account.currencies`, it must first be added to this global list, ensuring consistency and preventing the use of unsupported currencies across the system.

## Scalability Considerations

As the project grows and handles higher volumes of transactions and accounts, several strategies can be employed to ensure the system remains performant and reliable.

### 1. **Event-Driven Architecture with AWS EventBridge**

We are already leveraging AWS EventBridge to handle data interactions between the `accounts-service` and `reporting-service`. This event-driven architecture allows the system to decouple services, enabling each service to scale independently and react to events in real-time. As the volume of transactions increases, EventBridge can handle high throughput, ensuring reliable event delivery and processing without direct service-to-service communication.

### 2. **Database Sharding and Partitioning**

To manage large volumes of data, sharding or partitioning the database can be implemented. This involves splitting the data across multiple databases or tables based on criteria like account ID or geographic region, reducing the load on individual database instances and improving query performance.

### 3. **Asynchronous Processing with Queues**

For high volumes of transactions, implementing asynchronous processing using message queues (e.g., AWS SQS) in conjunction with EventBridge can help distribute the workload. Transactions can be processed in the background, allowing the system to handle large bursts of activity without overwhelming the backend services.

### 4. **Caching Frequently Accessed Data**

Implementing caching mechanisms, such as using Redis or Memcached, can significantly reduce the load on the database by storing frequently accessed data in memory. This is particularly useful for read-heavy operations, such as retrieving account details or transaction histories.

### 5. **Optimizing Database Queries**

As the volume of data grows, optimizing database queries becomes crucial. This can include creating indexes on frequently queried fields, using query optimizers, and avoiding expensive operations like full table scans. Additionally, database read replicas can be used to distribute read operations and reduce the load on the primary database.

### 6. **Rate Limiting and Throttling**

To prevent the system from being overwhelmed by excessive requests, rate limiting and throttling mechanisms can be implemented on services or on the lambda function level. This ensures that the system can handle high traffic without degrading performance, by controlling the number of requests each user or service can make within a specified time frame.

### 8. **Microservices Architecture**

Adopting a microservices architecture allows for each service (e.g., accounts and transactions) to be scaled independently based on demand. This modular approach also makes it easier to update or optimize individual services without affecting the entire system.

### 10. **Using Serverless layers**

To ensure uniformity and reduce code repetition it is advised to use Layers for common files in both services like `Events` or `valdiationMiddleware`. To make it more seamless, a third party middleware service might be used to ensure seamless integration and more proper error handling. 
   
