# Threshold USD API

## Installation & Setup

### Prerequisites
- Node.js (v16 or higher)
- npm or yarn
- PostgreSQL (if applicable)

### Installation Steps

1. Clone the repository
```bash
git clone [repository-url]
cd threshold-usd-api
```

2. Install dependencies
```bash
npm install
# or
yarn install
```

3. Environment Setup
Create a `.env` file in the root directory with the following variables:
```env
PORT=3000
NODE_ENV=development
# Add other required environment variables
```

4. Start the API
```bash
npm run start
# or
yarn start
```

### Health Check
Verify the installation by accessing:
```
GET http://localhost:3000/health
```

### Rate Limiting
```
Requests: 100 per minute
Reset: 60 seconds
```

### Request Examples

#### Login
```bash
curl -X POST http://localhost:3000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "yourpassword"
  }'
```

#### Mint USD
```bash
curl -X POST http://localhost:3000/api/v1/usd/mint \
  -H "Authorization: Bearer <your_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": "100",
    "collateralType": "ETH"
  }'
```

### Websocket Support
Real-time updates are available through WebSocket connection:
```
ws://localhost:3000/ws
```

## API Reference

### Base URL
```
http://localhost:3000/api/v1
```

### Authentication
All API requests require a Bearer token in the Authorization header:
```
Authorization: Bearer <your_token>
```

### Endpoints

#### Account Management
```http
POST   /auth/login          # Login with credentials
POST   /auth/register       # Register new account
GET    /auth/me            # Get current user info
```

#### USD Operations
```http
GET    /usd/balance        # Get USD balance
POST   /usd/mint           # Mint new USD
POST   /usd/redeem         # Redeem USD
GET    /usd/transactions   # Get transaction history
```

#### Collateral Management
```http
GET    /collateral/balance    # Get collateral balance
POST   /collateral/deposit    # Deposit collateral
POST   /collateral/withdraw   # Withdraw collateral
```

### Response Format
All responses follow this structure:
```json
{
  "success": true,
  "data": {},
  "error": null
}
```

### Error Codes
| Code | Description |
|------|-------------|
| 400  | Bad Request |
| 401  | Unauthorized |
| 403  | Forbidden |
| 404  | Not Found |
| 500  | Internal Server Error |