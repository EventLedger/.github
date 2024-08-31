
# Project Name

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

## Additional Information

- **Accounts Service**: Manages bank accounts, balances, and transactions, and publishes events to AWS EventBridge.
- **Reporting Service**: Listens to transaction events and generates monthly statements.

For more detailed documentation on each service, refer to the respective directories.
