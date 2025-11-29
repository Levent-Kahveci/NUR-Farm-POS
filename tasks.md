# Implementation Plan

- [x] 1. Set up project structure and dependencies





  - Initialize Node.js/Express backend with TypeScript
  - Initialize React frontend with TypeScript and Tailwind CSS
  - Set up PostgreSQL database
  - Configure build tools and development environment
  - Install Square SDK and other required dependencies
  - _Requirements: All_

- [ ] 2. Implement database schema and migrations
  - Create database tables for products, customers, orders, reviews, cart, payments, system settings, price history, butchers, cut orders, and butcher payments
  - Set up foreign key relationships and constraints
  - Create database indexes for performance
  - Write migration scripts
  - _Requirements: All_

- [ ] 3. Implement authentication and authorization system
  - Create user authentication with JWT tokens
  - Implement role-based access control (customer vs administrator)
  - Create login and registration endpoints
  - Add authentication middleware
  - _Requirements: All admin and customer requirements_

- [ ] 3.1 Write property test for authentication
  - **Property: Token generation and validation**
  - **Validates: Requirements 3.1**

- [ ] 4. Implement Product Service and API endpoints
- [ ] 4.1 Create Product data model and repository
  - Implement Product interface and database operations
  - Create CRUD methods for products
  - _Requirements: 7.1, 7.2, 7.3_

- [ ] 4.2 Write property test for product creation
  - **Property 19: Product creation**
  - **Validates: Requirements 7.1**

- [ ] 4.3 Implement product validation logic
  - Validate required fields and formats
  - Implement price validation
  - _Requirements: 7.4, 9.2_

- [ ] 4.4 Write property test for product validation
  - **Property 22: Product validation**
  - **Validates: Requirements 7.4**

- [ ] 4.5 Implement product soft delete
  - Mark products as inactive
  - Filter inactive products from customer catalog
  - _Requirements: 7.3_

- [ ] 4.6 Write property test for product soft delete
  - **Property 21: Product soft delete**
  - **Validates: Requirements 7.3**

- [ ] 4.7 Create product API endpoints
  - GET /api/products (list active products)
  - GET /api/products/:id (get product details)
  - POST /api/products (create product - admin)
  - PUT /api/products/:id (update product - admin)
  - DELETE /api/products/:id (soft delete - admin)
  - _Requirements: 1.1, 7.1, 7.2, 7.3_

- [ ] 4.8 Write property test for product update propagation
  - **Property 20: Product update propagation**
  - **Validates: Requirements 7.2**

- [ ] 5. Implement Inventory Management
- [ ] 5.1 Create inventory update functionality
  - Implement inventory quantity updates
  - Add immediate availability reflection
  - _Requirements: 8.1_

- [ ] 5.2 Write property test for inventory update immediacy
  - **Property 23: Inventory update immediacy**
  - **Validates: Requirements 8.1**

- [ ] 5.3 Implement inventory deduction on order placement
  - Decrement inventory when orders are placed
  - Implement transaction handling for atomicity
  - _Requirements: 8.2_

- [ ] 5.4 Write property test for order inventory deduction
  - **Property 24: Order inventory deduction**
  - **Validates: Requirements 8.2**

- [ ] 5.5 Implement automatic out-of-stock marking
  - Mark products as out of stock when inventory reaches zero
  - _Requirements: 8.3_

- [ ] 5.6 Write property test for automatic out-of-stock marking
  - **Property 25: Automatic out-of-stock marking**
  - **Validates: Requirements 8.3**

- [ ] 5.7 Implement non-negative inventory constraint
  - Add validation to prevent negative inventory
  - Handle concurrent inventory updates
  - _Requirements: 8.5_

- [ ] 5.8 Write property test for non-negative inventory invariant
  - **Property 27: Non-negative inventory invariant**
  - **Validates: Requirements 8.5**

- [ ] 5.9 Create inventory management API endpoints
  - PUT /api/admin/inventory/:id (update inventory)
  - GET /api/admin/inventory (view all inventory)
  - _Requirements: 8.1, 8.4_

- [ ] 5.10 Write property test for inventory display accuracy
  - **Property 26: Inventory display accuracy**
  - **Validates: Requirements 8.4**

- [ ] 6. Implement Pricing Management
- [ ] 6.1 Create price update functionality
  - Implement price updates with validation
  - Apply new prices to future orders only
  - _Requirements: 9.1, 9.2_

- [ ] 6.2 Write property test for price validation
  - **Property 29: Price validation**
  - **Validates: Requirements 9.2**

- [ ] 6.3 Implement price history tracking
  - Store price changes with timestamps
  - Create price history table entries
  - _Requirements: 9.3_

- [ ] 6.4 Write property test for price history maintenance
  - **Property 30: Price history maintenance**
  - **Validates: Requirements 9.3**

- [ ] 6.5 Implement price preservation for existing orders
  - Store price at purchase time in order items
  - Ensure existing orders retain original prices
  - _Requirements: 9.4_

- [ ] 6.6 Write property test for price update application
  - **Property 28: Price update application**
  - **Validates: Requirements 9.1, 9.4**

- [ ] 7. Implement Shopping Cart functionality
- [ ] 7.1 Create Cart data model and repository
  - Implement Cart and CartItem interfaces
  - Create cart persistence methods
  - _Requirements: 2.1, 2.4_

- [ ] 7.2 Implement add to cart functionality
  - Add items to cart with quantity
  - Validate against inventory availability
  - _Requirements: 2.1, 2.5_

- [ ] 7.3 Write property test for cart addition
  - **Property 3: Cart addition**
  - **Validates: Requirements 2.1**

- [ ] 7.4 Write property test for inventory limit enforcement
  - **Property 6: Inventory limit enforcement**
  - **Validates: Requirements 2.5**

- [ ] 7.5 Implement cart modification and removal
  - Update item quantities
  - Remove items from cart
  - Recalculate cart total
  - _Requirements: 2.2, 2.3_

- [ ] 7.6 Write property test for cart total recalculation
  - **Property 4: Cart total recalculation**
  - **Validates: Requirements 2.2, 2.3**

- [ ] 7.7 Implement cart persistence
  - Save cart state on updates
  - Retrieve cart for customer session
  - _Requirements: 2.4_

- [ ] 7.8 Write property test for cart persistence
  - **Property 5: Cart persistence**
  - **Validates: Requirements 2.4**

- [ ] 7.9 Implement out-of-stock prevention
  - Prevent adding out-of-stock products to cart
  - Display unavailable status
  - _Requirements: 1.2_

- [ ] 7.10 Write property test for out-of-stock prevention
  - **Property 2: Out-of-stock prevention**
  - **Validates: Requirements 1.2**

- [ ] 7.11 Create cart API endpoints
  - GET /api/cart (get current cart)
  - POST /api/cart/items (add item)
  - PUT /api/cart/items/:id (update quantity)
  - DELETE /api/cart/items/:id (remove item)
  - DELETE /api/cart (clear cart)
  - _Requirements: 2.1, 2.2, 2.3, 2.4_

- [ ] 8. Implement Delivery Service
- [ ] 8.1 Integrate geocoding API for distance calculation
  - Set up Google Maps Geocoding API
  - Implement address to coordinates conversion
  - Calculate distance between two coordinates
  - _Requirements: 3.1_

- [ ] 8.2 Write property test for distance calculation
  - **Property 7: Distance calculation**
  - **Validates: Requirements 3.1**

- [ ] 8.3 Implement delivery zone validation
  - Validate addresses against delivery radius
  - Accept addresses within range
  - Reject addresses outside range
  - _Requirements: 3.2, 3.3_

- [ ] 8.4 Write property test for delivery zone validation
  - **Property 8: Delivery zone validation**
  - **Validates: Requirements 3.2, 3.3**

- [ ] 8.5 Implement validation feedback
  - Return clear acceptance/rejection messages
  - Include distance information
  - _Requirements: 3.4_

- [ ] 8.6 Write property test for validation feedback
  - **Property 9: Validation feedback**
  - **Validates: Requirements 3.4**

- [ ] 8.7 Implement delivery slot management
  - Create delivery slot data model
  - Track slot capacity and bookings
  - Return available slots only
  - _Requirements: 4.1, 4.3_

- [ ] 8.8 Write property test for available slots display
  - **Property 10: Available slots display**
  - **Validates: Requirements 4.1**

- [ ] 8.9 Write property test for slot capacity management
  - **Property 12: Slot capacity management**
  - **Validates: Requirements 4.3**

- [ ] 8.10 Implement slot reservation
  - Associate delivery slot with order
  - Store delivery date and time window
  - _Requirements: 4.2, 4.4_

- [ ] 8.11 Write property test for slot reservation
  - **Property 11: Slot reservation**
  - **Validates: Requirements 4.2**

- [ ] 8.12 Write property test for delivery details persistence
  - **Property 13: Delivery details persistence**
  - **Validates: Requirements 4.4**

- [ ] 8.13 Create delivery API endpoints
  - POST /api/delivery/validate (validate address)
  - GET /api/delivery/slots (get available slots)
  - _Requirements: 3.1, 3.2, 3.3, 4.1_

- [ ] 9. Implement Payment Service with Square integration
- [ ] 9.1 Set up Square SDK and configuration
  - Configure Square API credentials
  - Initialize Square client
  - Set up sandbox for testing
  - _Requirements: 5.1_

- [ ] 9.2 Implement payment processing
  - Create payment request to Square
  - Transmit payment data securely
  - Handle payment responses
  - _Requirements: 5.2_

- [ ] 9.3 Write property test for payment data transmission
  - **Property 14: Payment data transmission**
  - **Validates: Requirements 5.2**

- [ ] 9.4 Implement payment approval handling
  - Process approved payments
  - Create confirmed orders
  - Display confirmation
  - _Requirements: 5.3_

- [ ] 9.5 Write property test for payment approval handling
  - **Property 15: Payment approval handling**
  - **Validates: Requirements 5.3**

- [ ] 9.6 Implement payment decline handling
  - Display error messages for declined payments
  - Maintain cart state for retry
  - _Requirements: 5.4_

- [ ] 9.7 Write property test for payment decline handling
  - **Property 16: Payment decline handling**
  - **Validates: Requirements 5.4**

- [ ] 9.8 Implement transaction reference storage
  - Store Square transaction ID with order
  - Create payment record
  - _Requirements: 5.5_

- [ ] 9.9 Write property test for transaction reference storage
  - **Property 17: Transaction reference storage**
  - **Validates: Requirements 5.5**

- [ ] 9.10 Create payment API endpoints
  - POST /api/payments/process (process payment)
  - GET /api/payments/:id (get payment details)
  - _Requirements: 5.1, 5.2, 5.3, 5.4, 5.5_

- [ ] 10. Implement Order Service
- [ ] 10.1 Create Order data model and repository
  - Implement Order and OrderItem interfaces
  - Create order persistence methods
  - Generate unique order numbers
  - _Requirements: 6.1, 6.3_

- [ ] 10.2 Implement order creation workflow
  - Create order from cart
  - Store order items with prices at purchase
  - Associate payment and delivery details
  - Trigger inventory deduction
  - _Requirements: 6.1, 6.3, 8.2, 9.4_

- [ ] 10.3 Write property test for order confirmation completeness
  - **Property 18: Order confirmation completeness**
  - **Validates: Requirements 6.1, 6.2, 6.3**

- [ ] 10.4 Implement order status management
  - Update order status
  - Record status change timestamps
  - _Requirements: 10.3_

- [ ] 10.5 Write property test for order status update tracking
  - **Property 32: Order status update tracking**
  - **Validates: Requirements 10.3**

- [ ] 10.6 Implement order retrieval and listing
  - Get order by ID
  - List orders for customer
  - List all orders for admin
  - _Requirements: 10.1, 10.4_

- [ ] 10.7 Write property test for order list completeness
  - **Property 31: Order list completeness**
  - **Validates: Requirements 10.1, 10.2, 10.4**

- [ ] 10.8 Create order API endpoints
  - POST /api/orders (create order)
  - GET /api/orders/:id (get order details)
  - GET /api/orders (list orders)
  - PUT /api/orders/:id/status (update status - admin)
  - _Requirements: 6.1, 6.3, 10.1, 10.3, 10.4_

- [ ] 11. Implement Review System
- [ ] 11.1 Create Review data model and repository
  - Implement Review interface
  - Create review persistence methods
  - _Requirements: 12.3_

- [ ] 11.2 Implement review authorization
  - Check if customer purchased product
  - Allow reviews only for purchased products
  - _Requirements: 12.1, 12.4_

- [ ] 11.3 Write property test for purchase-based review authorization
  - **Property 35: Purchase-based review authorization**
  - **Validates: Requirements 12.1, 12.4**

- [ ] 11.4 Implement review validation
  - Validate rating is between 1 and 5
  - Accept optional comment text
  - _Requirements: 12.2_

- [ ] 11.5 Write property test for review validation
  - **Property 36: Review validation**
  - **Validates: Requirements 12.2**

- [ ] 11.6 Implement review submission
  - Store review with all required fields
  - Replace existing review if customer reviews same product again
  - _Requirements: 12.3, 12.5_

- [ ] 11.7 Write property test for review persistence
  - **Property 37: Review persistence**
  - **Validates: Requirements 12.3**

- [ ] 11.8 Write property test for review replacement
  - **Property 38: Review replacement**
  - **Validates: Requirements 12.5**

- [ ] 11.9 Implement review retrieval and display
  - Get reviews for product
  - Sort reviews by date (most recent first)
  - Calculate average rating
  - _Requirements: 13.1, 13.2, 13.4, 13.5_

- [ ] 11.10 Write property test for average rating calculation
  - **Property 39: Average rating calculation**
  - **Validates: Requirements 13.1, 13.5**

- [ ] 11.11 Write property test for review display completeness
  - **Property 40: Review display completeness**
  - **Validates: Requirements 13.2, 13.4**

- [ ] 11.12 Create review API endpoints
  - GET /api/products/:id/reviews (get product reviews)
  - POST /api/products/:id/reviews (submit review)
  - _Requirements: 12.1, 12.2, 12.3, 13.1, 13.2, 13.4_

- [ ] 12. Implement Butcher Management System
- [ ] 12.1 Create Butcher, CutOrder, and ButcherPayment data models
  - Implement data interfaces
  - Create database operations
  - _Requirements: 14.1, 15.1_

- [ ] 12.2 Implement cut order creation
  - Store cut order with all fields
  - Generate unique cut order number
  - _Requirements: 14.1, 14.2_

- [ ] 12.3 Write property test for cut order creation
  - **Property 41: Cut order creation**
  - **Validates: Requirements 14.1, 14.2**

- [ ] 12.4 Implement cut order status management
  - Update cut order status
  - Record timestamps for status changes
  - Store completion date when completed
  - _Requirements: 14.4, 14.5_

- [ ] 12.5 Write property test for cut order status update
  - **Property 43: Cut order status update**
  - **Validates: Requirements 14.4, 14.5**

- [ ] 12.6 Implement cut order display
  - List all cut orders with status
  - Get cut order details
  - _Requirements: 14.3_

- [ ] 12.7 Write property test for cut order display
  - **Property 42: Cut order display**
  - **Validates: Requirements 14.3**

- [ ] 12.8 Implement butcher payment recording
  - Store payment with all fields
  - Mark cut order as paid
  - Validate payment amount
  - _Requirements: 15.1, 15.2, 15.4_

- [ ] 12.9 Write property test for butcher payment recording
  - **Property 44: Butcher payment recording**
  - **Validates: Requirements 15.1, 15.2**

- [ ] 12.10 Write property test for payment amount validation
  - **Property 46: Payment amount validation**
  - **Validates: Requirements 15.4**

- [ ] 12.11 Implement butcher payment display
  - List all payments with cut order details
  - _Requirements: 15.3_

- [ ] 12.12 Write property test for butcher payment display
  - **Property 45: Butcher payment display**
  - **Validates: Requirements 15.3**

- [ ] 12.13 Implement expense calculation
  - Calculate total expenses for time period
  - Sum all payments
  - _Requirements: 15.5_

- [ ] 12.14 Write property test for total expense calculation
  - **Property 47: Total expense calculation**
  - **Validates: Requirements 15.5**

- [ ] 12.15 Implement expense reporting
  - Generate reports grouped by time period
  - Filter by date range and meat type
  - Calculate metrics (count, total, average)
  - _Requirements: 16.1, 16.2, 16.3, 16.4_

- [ ] 12.16 Write property test for expense report generation
  - **Property 48: Expense report generation**
  - **Validates: Requirements 16.1**

- [ ] 12.17 Write property test for expense report filtering
  - **Property 49: Expense report filtering**
  - **Validates: Requirements 16.2**

- [ ] 12.18 Write property test for expense report metrics
  - **Property 50: Expense report metrics**
  - **Validates: Requirements 16.3**

- [ ] 12.19 Write property test for average cost calculation
  - **Property 51: Average cost calculation**
  - **Validates: Requirements 16.4**

- [ ] 12.20 Create butcher management API endpoints
  - GET /api/butchers (list butchers)
  - POST /api/butchers (create butcher)
  - PUT /api/butchers/:id (update butcher)
  - DELETE /api/butchers/:id (delete butcher)
  - GET /api/cut-orders (list cut orders)
  - POST /api/cut-orders (create cut order)
  - GET /api/cut-orders/:id (get cut order details)
  - PUT /api/cut-orders/:id (update cut order)
  - POST /api/butcher-payments (record payment)
  - GET /api/butcher-payments (list payments)
  - GET /api/reports/cut-expenses (get expense report)
  - _Requirements: 14.1, 14.2, 14.3, 14.4, 15.1, 15.3, 16.1_

- [ ] 13. Implement System Settings
- [ ] 13.1 Create SystemSettings data model
  - Implement settings interface
  - Create settings persistence methods
  - _Requirements: 11.1, 11.2_

- [ ] 13.2 Implement farm location configuration
  - Store farm coordinates
  - _Requirements: 11.1_

- [ ] 13.3 Write property test for farm location persistence
  - **Property 33: Farm location persistence**
  - **Validates: Requirements 11.1**

- [ ] 13.4 Implement delivery radius configuration
  - Store delivery radius
  - Apply to address validations
  - _Requirements: 11.2, 11.3_

- [ ] 13.5 Write property test for delivery radius application
  - **Property 34: Delivery radius application**
  - **Validates: Requirements 11.2, 11.3**

- [ ] 13.6 Create settings API endpoints
  - GET /api/admin/settings (get settings)
  - PUT /api/admin/settings (update settings)
  - _Requirements: 11.1, 11.2, 11.3_

- [ ] 14. Checkpoint - Ensure all backend tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 15. Build Customer Frontend
- [ ] 15.1 Create ProductCatalog component
  - Display product grid
  - Show product information
  - Filter out-of-stock products
  - _Requirements: 1.1, 1.2_

- [ ] 15.2 Write property test for product display completeness
  - **Property 1: Product display completeness**
  - **Validates: Requirements 1.1, 1.3, 1.4**

- [ ] 15.3 Create ProductDetail component
  - Display full product information
  - Show reviews and ratings
  - Add to cart functionality
  - Review submission form
  - _Requirements: 1.1, 2.1, 13.1, 13.2_

- [ ] 15.4 Create ShoppingCart component
  - Display cart items
  - Quantity modification
  - Item removal
  - Cart total display
  - Checkout button
  - _Requirements: 2.1, 2.2, 2.3_

- [ ] 15.5 Create CheckoutFlow component
  - Multi-step checkout process
  - Address entry and validation
  - Delivery slot selection
  - Square payment integration
  - Order confirmation display
  - _Requirements: 3.1, 3.2, 3.3, 4.1, 4.2, 5.1, 5.2, 5.3, 6.1_

- [ ] 15.6 Integrate Square Web Payments SDK
  - Initialize Square payment form
  - Handle payment submission
  - Display payment errors
  - _Requirements: 5.1, 5.2, 5.3, 5.4_

- [ ] 15.7 Create OrderConfirmation component
  - Display order summary
  - Show order number and details
  - _Requirements: 6.1, 6.2_

- [ ] 15.8 Write unit tests for customer components
  - Test ProductCatalog rendering
  - Test ShoppingCart calculations
  - Test CheckoutFlow validation
  - _Requirements: 1.1, 2.1, 3.1, 4.1, 5.1_

- [ ] 16. Build Admin Frontend
- [ ] 16.1 Create ProductManagement component
  - Product list with search
  - Add/Edit/Delete product forms
  - Image upload
  - _Requirements: 7.1, 7.2, 7.3_

- [ ] 16.2 Create InventoryManagement component
  - Inventory table
  - Quantity adjustment controls
  - Low stock alerts
  - _Requirements: 8.1, 8.4_

- [ ] 16.3 Create PricingManagement component
  - Price update interface
  - Price history view
  - _Requirements: 9.1, 9.3_

- [ ] 16.4 Create OrderManagement component
  - Order list with filtering
  - Order detail view
  - Status update controls
  - _Requirements: 10.1, 10.2, 10.3, 10.4_

- [ ] 16.5 Create ButcherManagement component
  - Butcher list
  - Cut order creation and management
  - Payment recording interface
  - _Requirements: 14.1, 14.2, 14.3, 14.4, 15.1, 15.2_

- [ ] 16.6 Create CutExpenseReports component
  - Expense reports by time period
  - Filtering controls
  - Summary statistics display
  - _Requirements: 16.1, 16.2, 16.3, 16.4_

- [ ] 16.7 Create SettingsManagement component
  - Farm location configuration
  - Delivery radius setting
  - _Requirements: 11.1, 11.2_

- [ ] 16.8 Write unit tests for admin components
  - Test ProductManagement CRUD operations
  - Test InventoryManagement updates
  - Test OrderManagement status changes
  - Test ButcherManagement operations
  - _Requirements: 7.1, 8.1, 10.3, 14.1_

- [ ] 17. Implement error handling and validation
  - Add client-side form validation
  - Implement error message display
  - Add network error handling
  - Implement retry logic
  - _Requirements: All_

- [ ] 18. Final Checkpoint - Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.
