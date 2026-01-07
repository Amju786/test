# Broker Adapter Pattern Implementation Guide

## Overview

StockSync now uses a **Broker Adapter Pattern** that allows seamless integration of multiple stock brokers. The system is designed to be extensible, secure, and maintainable.

## Architecture

### Components

1. **Base Adapter** (`lib/brokers/base-adapter.js`)
   - Abstract interface defining the contract for all brokers
   - Ensures consistent API across different brokers
   - Provides common validation methods

2. **Broker Implementations** (`lib/brokers/dhan.js`, etc.)
   - Concrete implementations extending `BaseBrokerAdapter`
   - Broker-specific API logic
   - Mapping functions for standardization

3. **Broker Factory** (`lib/brokers/broker-factory.js`)
   - Creates broker instances based on broker name
   - Manages broker registration
   - Provides connection helpers

4. **Security Utilities** (`lib/brokers/security.js`)
   - Token validation
   - Rate limiting
   - Error sanitization
   - Request validation

## Current Implementation: Dhan API

### Trading API

✅ **Implemented Methods:**
- `placeOrder()` - Place buy/sell orders
- `getOrderStatus()` - Check order status
- `cancelOrder()` - Cancel pending orders
- `modifyOrder()` - Modify existing orders
- `getHoldings()` - Get portfolio holdings
- `getFunds()` - Get available funds/margin
- `getPositions()` - Get current positions
- `getOrderHistory()` - Get order history

### Data API

✅ **Implemented Methods:**
- `getQuote()` - Get real-time market quote
- `getHistoricalData()` - Get historical OHLC data
- `searchInstruments()` - Search for stocks/instruments
- `getMarketDepth()` - Get order book/market depth

### Rate Limiting

Dhan API rate limits are enforced:
- **Order APIs**: 25 requests/second
- **Non-Trading APIs**: 20 requests/second
- **Data APIs**: 10 requests/second
- **Quote APIs**: 1 request/second

## Usage Examples

### Creating a Broker Instance

```javascript
import { BrokerFactory } from '@/lib/brokers/broker-factory';

// Create Dhan broker
const broker = BrokerFactory.createBroker('Dhan', {
  clientId: 'YOUR_CLIENT_ID',
  accessToken: 'YOUR_ACCESS_TOKEN'
});
```

### Placing an Order

```javascript
const orderParams = {
  transactionType: 'BUY',
  exchangeSegment: 'NSE_EQ',
  productType: 'CNC',
  orderType: 'MARKET',
  securityId: '2885',
  tradingSymbol: 'RELIANCE',
  quantity: 10
};

const result = await broker.placeOrder(orderParams);
console.log('Order ID:', result.orderId);
```

### Getting Market Data

```javascript
// Get real-time quote
const quote = await broker.getQuote('RELIANCE', 'NSE');

// Get historical data
const historical = await broker.getHistoricalData({
  symbol: 'RELIANCE',
  exchange: 'NSE',
  from: '2024-01-01',
  to: '2024-01-31',
  interval: '1D'
});

// Search instruments
const results = await broker.searchInstruments('RELIANCE');

// Get market depth
const depth = await broker.getMarketDepth('RELIANCE', 'NSE');
```

### Using from User Connection

```javascript
// Get broker from user's stored connection
const broker = BrokerFactory.createFromConnection(user.brokerConnection);

if (broker) {
  const holdings = await broker.getHoldings();
}
```

## API Endpoints

### Trading Endpoints

- `POST /api/admin/execute-order` - Execute orders (uses adapter)
- `POST /api/user/connect-broker` - Connect broker (validates via adapter)
- `GET /api/user/portfolio` - Get holdings (uses adapter)
- `GET /api/user/funds` - Get funds (uses adapter)
- `GET /api/user/orders` - Get order history

### Data API Endpoints

- `GET /api/data/quote?symbol=RELIANCE&exchange=NSE` - Get quote
- `GET /api/data/historical?symbol=RELIANCE&from=2024-01-01&to=2024-01-31` - Historical data
- `GET /api/data/search?q=RELIANCE` - Search instruments
- `GET /api/data/market-depth?symbol=RELIANCE&exchange=NSE` - Market depth

## Adding a New Broker

### Step 1: Create Broker Adapter

Create a new file `lib/brokers/zerodha.js`:

```javascript
import { BaseBrokerAdapter } from './base-adapter.js';

export class ZerodhaBrokerAdapter extends BaseBrokerAdapter {
  constructor(config) {
    super({
      brokerName: 'Zerodha',
      ...config
    });
    this.baseUrl = 'https://kite.zerodha.com';
    // Initialize Zerodha-specific config
  }

  async placeOrder(orderParams) {
    // Implement Zerodha order placement
    // Map standard params to Zerodha format
  }

  async getQuote(symbol, exchange) {
    // Implement Zerodha quote API
  }

  // ... implement all required methods from BaseBrokerAdapter

  getConstants() {
    return ZERODHA_CONSTANTS;
  }

  mapProductType(productType) {
    // Map standard product types to Zerodha format
  }

  mapExchangeSegment(exchange) {
    // Map standard exchanges to Zerodha segments
  }
}
```

### Step 2: Register in Factory

Update `lib/brokers/broker-factory.js`:

```javascript
import { ZerodhaBrokerAdapter } from './zerodha.js';

export class BrokerFactory {
  static brokers = {
    'Dhan': DhanBrokerAdapter,
    'Zerodha': ZerodhaBrokerAdapter, // Add here
  };
  // ... rest of the code
}
```

### Step 3: Test

```javascript
const broker = BrokerFactory.createBroker('Zerodha', {
  apiKey: 'YOUR_API_KEY',
  accessToken: 'YOUR_ACCESS_TOKEN'
});

await broker.validateConnection();
```

## Security Features

### 1. Token Validation
- Validates token format before use
- Prevents invalid tokens from reaching broker APIs

### 2. Error Sanitization
- Removes sensitive data from error messages
- Prevents token/credentials leakage in logs

### 3. Rate Limiting
- Built-in rate limiting per endpoint
- Prevents API abuse
- Respects broker-specific limits

### 4. Request Validation
- Validates order parameters before execution
- Prevents invalid orders from being placed
- Provides clear error messages

## Standardization

The adapter pattern standardizes:

### Product Types
- `CNC` - Cash and Carry (Delivery)
- `INTRADAY` / `MIS` - Intraday
- `MARGIN` - Margin trading
- `MTF` - Margin Trading Facility

### Exchange Segments
- `NSE` - National Stock Exchange
- `BSE` - Bombay Stock Exchange
- `NSE_FNO` - NSE Futures & Options
- `BSE_FNO` - BSE Futures & Options

### Order Types
- `MARKET` - Market order
- `LIMIT` - Limit order
- `STOP_LOSS` - Stop loss order
- `STOP_LOSS_MARKET` - Stop loss market order

## Error Handling

All broker methods throw errors that are:
1. Sanitized (no sensitive data)
2. Descriptive (clear error messages)
3. Consistent (same format across brokers)

```javascript
try {
  await broker.placeOrder(orderParams);
} catch (error) {
  // Error is already sanitized
  console.error(error.message);
}
```

## Best Practices

1. **Always validate** broker connection before use
2. **Use factory** to create broker instances
3. **Handle errors** gracefully with fallbacks
4. **Respect rate limits** - use built-in rate limiter
5. **Sanitize errors** - never expose tokens/credentials
6. **Test connections** - use `validateConnection()` method

## Testing

```javascript
// Test broker connection
const broker = BrokerFactory.createBroker('Dhan', config);
const isValid = await broker.validateConnection();

// Test order validation
const validation = RequestValidator.validateOrder(orderParams);
if (!validation.valid) {
  console.error(validation.error);
}
```

## Future Brokers

The adapter pattern makes it easy to add:
- **Zerodha** - Kite Connect API
- **Upstox** - Upstox API
- **Fyers** - Fyers API
- **Groww** - Groww API
- **Angel One** - Angel One API
- **ICICI Direct** - ICICI Direct API

Each broker just needs to implement the `BaseBrokerAdapter` interface!

## Support

For issues or questions:
1. Check broker-specific documentation
2. Review error messages (they're sanitized but descriptive)
3. Validate connection and credentials
4. Check rate limits

---

**Note**: This implementation follows the Adapter Pattern from the Gang of Four design patterns, making it easy to integrate new brokers without changing existing code.

