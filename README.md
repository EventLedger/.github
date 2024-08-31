
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

For more detailed documentation on each service, refer to the respective directories.


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
   
