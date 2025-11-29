# Farm POS System - Design Document

## Overview

The Farm POS System is a web-based point of sale application that enables direct-to-consumer sales of farm products. The system consists of two primary user interfaces: a customer-facing storefront for browsing products, placing orders, and managing deliveries, and an administrative interface for managing products, inventory, pricing, and orders.

The architecture follows a modern web application pattern with a React-based frontend, Node.js/Express backend API, PostgreSQL database for persistent storage, and integration with Square's payment processing API. The system implements JWT-based authentication to secure customer accounts and protect sensitive operations. Customers must register and log in to place orders, view order status, and access payment information. The system implements geographic validation to ensure customers are within the 50-mile delivery radius and provides real-time inventory management to prevent overselling. Customers can access their complete order history for up to 5 years, with the ability to filter by year.

## Architecture

### High-Level Architecture

```
┌────────────────────────────────────────────────────────────┐
│                     Client Layer                           │
│  ┌──────────────────────┐    ┌──────────────────────┐      │
│  │  Customer Frontend   │    │   Admin Frontend     │      │
│  │    (Sencha ExtJS)    │    │   (Sencha ExtJS)     │      │
│  └──────────────────────┘    └──────────────────────┘      │
└────────────────────────────────────────────────────────────┘
                            │
                            │ HTTPS/REST API
                            ▼
┌───────────────────────────────────────────────────────────┐
│                     API Layer                             │
│  ┌─────────────────────────────────────────────────────┐  │
│  │           Express.js REST API Server                │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐     │  │
│  │  │  Product   │  │   Order    │  │  Payment   │     │  │
│  │  │  Service   │  │  Service   │  │  Service   │     │  │
│  │  └────────────┘  └────────────┘  └────────────┘     │  │
│  └─────────────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────────────┘
                            │
                ┌───────────┴───────────┐
                ▼                       ▼
┌──────────────────────────┐  ┌──────────────────────┐
│   Database Layer         │  │  External Services   │
│  ┌────────────────────┐  │  │  ┌────────────────┐  │
│  │   PostgreSQL DB    │  │  │  │  Square API    │  │
│  │                    │  │  │  │  (Payments)    │  │
│  │  - Products        │  │  │  └────────────────┘  │
│  │  - Orders          │  │  │  ┌────────────────┐  │
│  │  - Customers       │  │  │  │  Geocoding API │  │
│  │  - Reviews         │  │  │  │  (Distance)    │  │
│  │  - Inventory       │  │  │  └────────────────┘  │
│  └────────────────────┘  │  └──────────────────────┘
└──────────────────────────┘
```

### Technology Stack

**Frontend:**
- Sencha EXTJS for front end
- Java Router for navigation
- Ajax for API communication
- Tailwind CSS for styling
- Square Web Payments SDK for payment UI

**Backend:**
- Node.js with Express.js
- TypeScript
- PostgreSQL with pg library
- Square SDK for payment processing
- JSON Web Tokens (JWT) for authentication

**External Services:**
- Square Payment API for credit card processing
- Google Maps Geocoding API for distance calculation

## Components and Interfaces

### Frontend Components

#### Customer Interface

**Registration Component**
- User registration form with email, password, first name, last name, and phone number
- Password strength validation
- Email format validation
- Duplicate email detection
- Account creation confirmation

**Login Component**
- Email and password input fields
- Authentication error handling
- "Remember me" option
- Password reset link
- Redirect to intended page after login

**OrderHistory Component**
- Year selector dropdown (past 5 years)
- Order list display with filtering by year
- Order details including order number, date, items, total, delivery address, and status
- Chronological sorting (most recent first)
- Empty state message for years with no orders
- Order detail view modal

**ProductCatalog Component**
- Displays grid of available products
- Shows product images, names, prices, and availability
- Filters out-of-stock items
- Integrates with ProductCard component

**ProductCard Component**
- Displays individual product information
- Shows average rating and review count
- "Add to Cart" button with quantity selector
- Links to ProductDetail view

**ProductDetail Component**
- Full product information display
- Customer reviews and ratings list
- Review submission form (for purchased products)
- Add to cart functionality

**ShoppingCart Component**
- Lists cart items with quantities and prices
- Allows quantity modification and item removal
- Displays cart total
- Checkout button

**CheckoutFlow Component**
- Multi-step checkout process:
  1. Delivery address entry and validation
  2. Delivery date/time selection
  3. Payment information via Square
  4. Order confirmation
- Progress indicator
- Form validation

**OrderConfirmation Component**
- Displays order summary
- Shows order number and delivery details
- Provides order tracking information

#### Admin Interface

**ProductManagement Component**
- Product list with search and filter
- Add/Edit/Delete product functionality
- Inline editing for quick updates
- Image upload capability

**InventoryManagement Component**
- Product inventory table
- Quick quantity adjustment controls
- Low stock alerts
- Bulk update capability

**PricingManagement Component**
- Price update interface
- Price history view
- Bulk pricing tools

**OrderManagement Component**
- Order list with filtering by status
- Order detail view
- Status update controls
- Delivery schedule view

**SettingsManagement Component**
- Farm location configuration
- Delivery radius setting
- Business hours configuration

**ButcherManagement Component**
- Butcher list with add/edit/delete functionality
- Cut order creation and management
- Cut order status tracking
- Payment recording interface

**CutExpenseReports Component**
- Expense reports by time period
- Filtering by date range and meat type
- Summary statistics (total expenses, order count, average cost)
- Export functionality

### Backend API Endpoints

#### Authentication Endpoints
```
POST   /api/auth/register         - Register new customer account
POST   /api/auth/login            - Authenticate user and return JWT token
POST   /api/auth/logout           - Invalidate user session
GET    /api/auth/me               - Get current authenticated user info
POST   /api/auth/refresh          - Refresh JWT token
```

#### Product Endpoints
```
GET    /api/products              - List all active products
GET    /api/products/:id          - Get product details
POST   /api/products              - Create product (admin)
PUT    /api/products/:id          - Update product (admin)
DELETE /api/products/:id          - Soft delete product (admin)
GET    /api/products/:id/reviews  - Get product reviews
POST   /api/products/:id/reviews  - Submit product review
```

#### Cart Endpoints
```
GET    /api/cart                  - Get current cart (authenticated)
POST   /api/cart/items            - Add item to cart (authenticated)
PUT    /api/cart/items/:id        - Update cart item quantity (authenticated)
DELETE /api/cart/items/:id        - Remove item from cart (authenticated)
DELETE /api/cart                  - Clear cart (authenticated)
```

#### Order Endpoints
```
POST   /api/orders                - Create order (authenticated)
GET    /api/orders/:id            - Get order details (authenticated)
GET    /api/orders                - List orders (admin/customer, authenticated)
GET    /api/orders/history/:year  - Get order history by year (authenticated, customer)
PUT    /api/orders/:id/status     - Update order status (admin)
```

#### Delivery Endpoints
```
POST   /api/delivery/validate     - Validate delivery address
GET    /api/delivery/slots        - Get available delivery slots
```

#### Payment Endpoints
```
POST   /api/payments/process      - Process payment via Square
GET    /api/payments/:id          - Get payment details
```

#### Admin Endpoints
```
PUT    /api/admin/inventory/:id   - Update product inventory
GET    /api/admin/settings        - Get system settings
PUT    /api/admin/settings        - Update system settings
```

#### Butcher Management Endpoints
```
GET    /api/butchers              - List all butchers (admin)
POST   /api/butchers              - Create butcher (admin)
PUT    /api/butchers/:id          - Update butcher (admin)
DELETE /api/butchers/:id          - Delete butcher (admin)
GET    /api/cut-orders            - List cut orders (admin)
POST   /api/cut-orders            - Create cut order (admin)
GET    /api/cut-orders/:id        - Get cut order details (admin)
PUT    /api/cut-orders/:id        - Update cut order (admin)
POST   /api/butcher-payments      - Record butcher payment (admin)
GET    /api/butcher-payments      - List butcher payments (admin)
GET    /api/reports/cut-expenses  - Get cut expense report (admin)
```

### Service Layer

**ProductService**
- CRUD operations for products
- Inventory management
- Price history tracking
- Product availability checks

**OrderService**
- Order creation and management
- Order status workflow
- Inventory reservation and deduction
- Order history queries

**PaymentService**
- Square API integration
- Payment processing
- Transaction recording
- Refund handling

**DeliveryService**
- Address validation
- Distance calculation using geocoding
- Delivery slot management
- Delivery scheduling

**ReviewService**
- Review submission and validation
- Rating calculation
- Review retrieval and filtering

**AuthService**
- User authentication
- JWT token generation and validation
- Role-based access control

**ButcherService**
- Butcher CRUD operations
- Cut order management
- Cut order status workflow
- Payment recording and tracking
- Expense report generation

## Data Models

### Product
```typescript
interface Product {
  id: string;
  name: string;
  description: string;
  price: number;
  inventoryQuantity: number;
  imageUrl?: string;
  isActive: boolean;
  createdAt: Date;
  updatedAt: Date;
}
```

### Order
```typescript
interface Order {
  id: string;
  customerId: string;
  orderNumber: string;
  items: OrderItem[];
  subtotal: number;
  tax: number;
  total: number;
  status: OrderStatus;
  deliveryAddress: Address;
  deliveryDate: Date;
  deliveryTimeWindow: string;
  paymentId: string;
  createdAt: Date;
  updatedAt: Date;
}

interface OrderItem {
  productId: string;
  productName: string;
  quantity: number;
  priceAtPurchase: number;
  subtotal: number;
}

enum OrderStatus {
  PENDING = 'pending',
  CONFIRMED = 'confirmed',
  PREPARING = 'preparing',
  OUT_FOR_DELIVERY = 'out_for_delivery',
  DELIVERED = 'delivered',
  CANCELLED = 'cancelled'
}
```

### Customer
```typescript
interface Customer {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  phone: string;
  passwordHash: string;
  role: 'customer' | 'administrator';
  createdAt: Date;
  updatedAt: Date;
}
```

### AuthToken
```typescript
interface AuthToken {
  token: string;
  expiresAt: Date;
  userId: string;
}

interface LoginResponse {
  token: string;
  user: {
    id: string;
    email: string;
    firstName: string;
    lastName: string;
    role: string;
  };
}
```

### Address
```typescript
interface Address {
  street: string;
  city: string;
  state: string;
  zipCode: string;
  latitude?: number;
  longitude?: number;
}
```

### Review
```typescript
interface Review {
  id: string;
  productId: string;
  customerId: string;
  orderId: string;
  rating: number; // 1-5
  comment?: string;
  createdAt: Date;
  updatedAt: Date;
}
```

### Cart
```typescript
interface Cart {
  id: string;
  customerId: string;
  items: CartItem[];
  createdAt: Date;
  updatedAt: Date;
}

interface CartItem {
  productId: string;
  quantity: number;
}
```

### Payment
```typescript
interface Payment {
  id: string;
  orderId: string;
  squarePaymentId: string;
  amount: number;
  status: PaymentStatus;
  createdAt: Date;
}

enum PaymentStatus {
  PENDING = 'pending',
  COMPLETED = 'completed',
  FAILED = 'failed',
  REFUNDED = 'refunded'
}
```

### SystemSettings
```typescript
interface SystemSettings {
  id: string;
  farmLatitude: number;
  farmLongitude: number;
  deliveryRadiusMiles: number;
  updatedAt: Date;
}
```

### PriceHistory
```typescript
interface PriceHistory {
  id: string;
  productId: string;
  price: number;
  effectiveDate: Date;
}
```

### Butcher
```typescript
interface Butcher {
  id: string;
  name: string;
  contactInfo: string;
  isActive: boolean;
  createdAt: Date;
  updatedAt: Date;
}
```

### CutOrder
```typescript
interface CutOrder {
  id: string;
  cutOrderNumber: string;
  butcherId: string;
  meatType: string;
  quantity: number;
  cutSpecifications: string;
  status: CutOrderStatus;
  orderDate: Date;
  completionDate?: Date;
  createdAt: Date;
  updatedAt: Date;
}

enum CutOrderStatus {
  PENDING = 'pending',
  IN_PROGRESS = 'in_progress',
  COMPLETED = 'completed',
  CANCELLED = 'cancelled'
}
```

### ButcherPayment
```typescript
interface ButcherPayment {
  id: string;
  cutOrderId: string;
  amount: number;
  paymentDate: Date;
  paymentMethod: string;
  notes?: string;
  createdAt: Date;
}
```


## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system-essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Product Catalog Properties

**Property 1: Product display completeness**
*For any* active product in the catalog, the displayed product information should include name, description, price (accurate to the cent), available quantity, and image URL if present.
**Validates: Requirements 1.1, 1.3, 1.4**

**Property 2: Out-of-stock prevention**
*For any* product with zero inventory quantity, attempting to add it to the cart should be rejected and the product should be marked as unavailable.
**Validates: Requirements 1.2**

### Shopping Cart Properties

**Property 3: Cart addition**
*For any* valid product and quantity within available inventory, adding the item to the cart should result in the cart containing that item with the specified quantity.
**Validates: Requirements 2.1**

**Property 4: Cart total recalculation**
*For any* cart modification (quantity change or item removal), the cart total should equal the sum of (quantity × price) for all remaining items.
**Validates: Requirements 2.2, 2.3**

**Property 5: Cart persistence**
*For any* cart update operation, retrieving the cart immediately afterward should return the updated state.
**Validates: Requirements 2.4**

**Property 6: Inventory limit enforcement**
*For any* product with inventory quantity N, attempting to add more than N items to the cart should be rejected with an error message.
**Validates: Requirements 2.5**

### Delivery Validation Properties

**Property 7: Distance calculation**
*For any* delivery address, the system should calculate and return the distance in miles from the configured farm location.
**Validates: Requirements 3.1**

**Property 8: Delivery zone validation**
*For any* delivery address, the address should be accepted if and only if the calculated distance is less than or equal to the configured delivery radius.
**Validates: Requirements 3.2, 3.3**

**Property 9: Validation feedback**
*For any* address validation attempt, the system should return a clear indication of whether delivery is available (accepted/rejected with reason).
**Validates: Requirements 3.4**

### Delivery Scheduling Properties

**Property 10: Available slots display**
*For any* delivery scheduling request, the system should return only time slots that are not fully booked.
**Validates: Requirements 4.1**

**Property 11: Slot reservation**
*For any* selected delivery slot, confirming the order should associate that slot with the order record.
**Validates: Requirements 4.2**

**Property 12: Slot capacity management**
*For any* delivery slot, once the maximum number of orders is reached, the slot should no longer appear in available slots.
**Validates: Requirements 4.3**

**Property 13: Delivery details persistence**
*For any* order with confirmed delivery details, the stored order should contain the delivery date and time window.
**Validates: Requirements 4.4**

### Payment Processing Properties

**Property 14: Payment data transmission**
*For any* payment request, the system should transmit the correct amount and order details to the Square Payment Gateway.
**Validates: Requirements 5.2**

**Property 15: Payment approval handling**
*For any* approved payment response from Square, the system should create a confirmed order and display confirmation to the customer.
**Validates: Requirements 5.3**

**Property 16: Payment decline handling**
*For any* declined payment response from Square, the system should display an error message and maintain the cart state for retry.
**Validates: Requirements 5.4**

**Property 17: Transaction reference storage**
*For any* completed payment transaction, the system should store the Square transaction ID with the order record.
**Validates: Requirements 5.5**

### Order Confirmation Properties

**Property 18: Order confirmation completeness**
*For any* successfully placed order, the system should generate and persist a confirmation containing order number, all items with quantities and prices, total, and delivery details.
**Validates: Requirements 6.1, 6.2, 6.3**

### Product Management Properties

**Property 19: Product creation**
*For any* valid product data (name, description, price, inventory), creating a product should result in a stored product with all specified fields.
**Validates: Requirements 7.1**

**Property 20: Product update propagation**
*For any* product update, retrieving the product immediately afterward should return the updated information.
**Validates: Requirements 7.2**

**Property 21: Product soft delete**
*For any* product deletion, the product should be marked as inactive and should not appear in the customer catalog.
**Validates: Requirements 7.3**

**Property 22: Product validation**
*For any* product creation or update with missing required fields or invalid formats, the operation should be rejected with a validation error.
**Validates: Requirements 7.4**

### Inventory Management Properties

**Property 23: Inventory update immediacy**
*For any* inventory quantity update, querying product availability immediately afterward should reflect the new quantity.
**Validates: Requirements 8.1**

**Property 24: Order inventory deduction**
*For any* order containing N units of a product with inventory quantity M, completing the order should result in inventory quantity M-N.
**Validates: Requirements 8.2**

**Property 25: Automatic out-of-stock marking**
*For any* product, when inventory reaches exactly zero through any operation, the product should be automatically marked as out of stock.
**Validates: Requirements 8.3**

**Property 26: Inventory display accuracy**
*For any* product in the inventory view, the displayed quantity should match the current inventory quantity in the database.
**Validates: Requirements 8.4**

**Property 27: Non-negative inventory invariant**
*For any* inventory operation, the resulting inventory quantity should never be negative.
**Validates: Requirements 8.5**

### Pricing Management Properties

**Property 28: Price update application**
*For any* price change, orders created after the change should use the new price while orders created before should retain the original price.
**Validates: Requirements 9.1, 9.4**

**Property 29: Price validation**
*For any* price update with a non-positive value, the update should be rejected with a validation error.
**Validates: Requirements 9.2**

**Property 30: Price history maintenance**
*For any* price change, the system should create a price history record with the new price and timestamp.
**Validates: Requirements 9.3**

### Order Management Properties

**Property 31: Order list completeness**
*For any* order in the system, the order management view should display order number, customer information, items, total, delivery details, and current status.
**Validates: Requirements 10.1, 10.2, 10.4**

**Property 32: Order status update tracking**
*For any* order status change, the system should store the new status with a timestamp.
**Validates: Requirements 10.3**

### System Configuration Properties

**Property 33: Farm location persistence**
*For any* farm location update, retrieving the system settings should return the updated coordinates.
**Validates: Requirements 11.1**

**Property 34: Delivery radius application**
*For any* delivery radius configuration change, all subsequent address validations should use the new radius value.
**Validates: Requirements 11.2, 11.3**

### Review System Properties

**Property 35: Purchase-based review authorization**
*For any* customer and product, the customer should be able to submit a review if and only if they have received an order containing that product.
**Validates: Requirements 12.1, 12.4**

**Property 36: Review validation**
*For any* review submission, the system should require a rating between 1 and 5 (inclusive) and should accept optional comment text.
**Validates: Requirements 12.2**

**Property 37: Review persistence**
*For any* submitted review, the stored review should contain customer ID, product ID, order ID, rating, comment (if provided), and timestamp.
**Validates: Requirements 12.3**

**Property 38: Review replacement**
*For any* customer submitting multiple reviews for the same product, only the most recent review should be stored and displayed.
**Validates: Requirements 12.5**

**Property 39: Average rating calculation**
*For any* product with N reviews having ratings r1, r2, ..., rN, the displayed average rating should equal (r1 + r2 + ... + rN) / N.
**Validates: Requirements 13.1, 13.5**

**Property 40: Review display completeness**
*For any* product with reviews, the displayed reviews should include rating, comment text (if present), and date, sorted with most recent first.
**Validates: Requirements 13.2, 13.4**

### Butcher Management Properties

**Property 41: Cut order creation**
*For any* valid cut order data (butcher ID, meat type, quantity, cut specifications), creating a cut order should result in a stored order with a unique cut order number and all specified fields.
**Validates: Requirements 14.1, 14.2**

**Property 42: Cut order display**
*For any* cut order in the system, the cut order management view should display the order with its current status.
**Validates: Requirements 14.3**

**Property 43: Cut order status update**
*For any* cut order status change, the system should store the new status with a timestamp, and if completed, should store the completion date.
**Validates: Requirements 14.4, 14.5**

**Property 44: Butcher payment recording**
*For any* valid payment data (cut order reference, amount, payment date, payment method), recording a payment should store all fields and mark the associated cut order as paid.
**Validates: Requirements 15.1, 15.2**

**Property 45: Butcher payment display**
*For any* butcher payment in the system, the payment view should display the payment with associated cut order details and amount.
**Validates: Requirements 15.3**

**Property 46: Payment amount validation**
*For any* payment amount that is not a positive monetary value, the payment recording should be rejected with a validation error.
**Validates: Requirements 15.4**

**Property 47: Total expense calculation**
*For any* time period with N payments having amounts p1, p2, ..., pN, the total butcher expenses should equal p1 + p2 + ... + pN.
**Validates: Requirements 15.5**

**Property 48: Expense report generation**
*For any* date range filter, the expense report should display total expenses grouped by the specified time period.
**Validates: Requirements 16.1**

**Property 49: Expense report filtering**
*For any* expense report with date range and meat type filters applied, only cut orders matching the filters should be included in the calculations.
**Validates: Requirements 16.2**

**Property 50: Expense report metrics**
*For any* expense report, the displayed metrics should include the number of cut orders and total payment amounts for the filtered data.
**Validates: Requirements 16.3**

**Property 51: Average cost calculation**
*For any* expense report with N cut orders and total expenses E, the average cost per cut order should equal E / N.
**Validates: Requirements 16.4**

### Authentication and Authorization Properties

**Property 52: User registration**
*For any* valid registration data (unique email, password, first name, last name, phone), creating an account should result in a stored customer with securely hashed password.
**Validates: Requirements 17.1, 17.2**

**Property 53: Duplicate email rejection**
*For any* registration attempt with an email that already exists in the system, the registration should be rejected with an error message.
**Validates: Requirements 17.3**

**Property 54: Authentication success**
*For any* registered user with valid credentials, authentication should succeed and return a valid JWT token.
**Validates: Requirements 17.4**

**Property 55: Protected resource access control**
*For any* request to ordering, order status, or payment information endpoints without valid authentication, the system should reject the request and redirect to login.
**Validates: Requirements 17.5, 17.6, 17.7**

**Property 56: Session persistence**
*For any* authenticated user session, the session should remain valid until explicit logout or token expiration.
**Validates: Requirements 17.8**

### Order History Properties

**Property 57: Order history year filtering**
*For any* authenticated customer and selected year, the order history should display only orders placed by that customer during the selected year.
**Validates: Requirements 18.2, 18.7**

**Property 58: Order history completeness**
*For any* order in the history view, the displayed information should include order number, order date, items, total amount, delivery address, and order status.
**Validates: Requirements 18.3**

**Property 59: Order history time limit**
*For any* order history request, only orders from the past 5 years from the current date should be available for display.
**Validates: Requirements 18.4**

**Property 60: Order history chronological sorting**
*For any* order history display, orders should be sorted by date with the most recent orders appearing first.
**Validates: Requirements 18.6**

**Property 61: Order history isolation**
*For any* authenticated customer viewing order history, only orders belonging to that customer should be displayed, never orders from other customers.
**Validates: Requirements 18.7**

## Error Handling

### Client-Side Error Handling

**Form Validation**
- Real-time validation for all user inputs
- Clear error messages displayed inline
- Prevention of form submission with invalid data

**Network Error Handling**
- Retry logic for failed API requests
- User-friendly error messages for network issues
- Graceful degradation when services are unavailable

**Payment Error Handling**
- Specific error messages for different payment failure types
- Ability to retry payment without losing cart state
- Timeout handling for payment processing

### Server-Side Error Handling

**Input Validation**
- Comprehensive validation of all API inputs
- Structured error responses with error codes and messages
- Prevention of invalid data from reaching the database

**Database Error Handling**
- Transaction rollback on failures
- Proper handling of constraint violations
- Connection pool management and retry logic

**External Service Error Handling**
- Timeout configuration for Square API calls
- Fallback behavior when geocoding service is unavailable
- Logging of all external service errors for debugging

**Inventory Concurrency**
- Optimistic locking to prevent overselling
- Transaction isolation for inventory updates
- Clear error messages when products become unavailable during checkout

### Error Response Format

All API errors follow a consistent format:
```typescript
interface ErrorResponse {
  error: {
    code: string;
    message: string;
    details?: any;
  }
}
```

Common error codes:
- `VALIDATION_ERROR`: Invalid input data
- `NOT_FOUND`: Resource not found
- `UNAUTHORIZED`: Authentication required
- `FORBIDDEN`: Insufficient permissions
- `INVALID_CREDENTIALS`: Login credentials are incorrect
- `DUPLICATE_EMAIL`: Email already registered
- `TOKEN_EXPIRED`: JWT token has expired
- `INVENTORY_UNAVAILABLE`: Product out of stock
- `PAYMENT_FAILED`: Payment processing error
- `DELIVERY_OUT_OF_RANGE`: Address outside delivery zone

## Testing Strategy

### Unit Testing

The system will use Jest as the testing framework for both frontend and backend unit tests.

**Backend Unit Tests:**
- Service layer methods (ProductService, OrderService, etc.)
- API endpoint handlers
- Data validation functions
- Business logic calculations (cart totals, distance calculations, rating averages)
- Error handling paths

**Frontend Unit Tests:**
- Component rendering with various props
- User interaction handlers
- Form validation logic
- State management functions
- Utility functions

### Property-Based Testing

Property-based testing will be implemented using fast-check library for JavaScript/TypeScript. Each correctness property defined in this document will be implemented as a property-based test.

**Configuration:**
- Minimum 100 iterations per property test
- Each property test must include a comment tag referencing the design document property
- Tag format: `// Feature: farm-pos, Property {number}: {property_text}`

**Test Generators:**
Property tests will use custom generators for domain objects:
- Product generator (with valid/invalid variations)
- Order generator (with various states and dates)
- Address generator (within and outside delivery range)
- Review generator (with valid ratings)
- Cart generator (with various item combinations)
- User credentials generator (with valid/invalid emails and passwords)
- JWT token generator (with various expiration states)

**Key Property Tests:**
- Cart total calculation across random cart modifications
- Inventory deduction across random order sequences
- Distance validation across random addresses
- Price preservation across price changes and order timing
- Review rating calculation across random review sets
- Non-negative inventory invariant across all operations
- Authentication token generation and validation across random user credentials
- Order history filtering by year across random order dates
- Protected endpoint access control across authenticated and unauthenticated requests

### Integration Testing

**API Integration Tests:**
- End-to-end flows through API endpoints
- Database transaction behavior
- Authentication and authorization flows

**External Service Integration:**
- Square payment processing (using Square sandbox)
- Geocoding API integration (with mock fallback)

### End-to-End Testing

Using Playwright for browser automation:
- User registration and login flow
- Complete customer purchase flow (with authentication)
- Order history viewing and filtering by year
- Admin product and inventory management flow
- Review submission and display flow
- Protected route access without authentication
- Error scenarios (payment failures, out-of-stock, delivery range, invalid credentials)

### Test Data Management

- Seed data scripts for development and testing
- Database reset utilities for test isolation
- Mock data generators for consistent test scenarios
