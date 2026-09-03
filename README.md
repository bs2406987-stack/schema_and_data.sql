# schema_and_data.s-- ====================================================================
-- 1. DROP EXISTING TABLES (For clean environment provisioning)
-- ====================================================================
DROP TABLE IF EXISTS order_items;
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS products;
DROP TABLE IF EXISTS users;

-- ====================================================================
-- 2. CREATE SCHEMA TABLES WITH CONSTRAINTS
-- ====================================================================
CREATE TABLE users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    country VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(100) NOT NULL,
    category VARCHAR(50) NOT NULL,
    price DECIMAL(10, 2) NOT NULL CHECK (price > 0),
    stock_quantity INT NOT NULL DEFAULT 0
);

CREATE TABLE orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(20) DEFAULT 'Pending',
    total_amount DECIMAL(10, 2) DEFAULT 0.00,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);

CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    unit_price DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- ====================================================================
-- 3. POPULATE MOCK DATA
-- ====================================================================
INSERT INTO users (first_name, last_name, email, country, created_at) VALUES
('Alice', 'Smith', 'alice@example.com', 'United States', '2025-01-15 10:00:00'),
('Bob', 'Johnson', 'bob@example.com', 'Canada', '2025-02-20 11:30:00'),
('Charlie', 'Brown', 'charlie@example.com', 'United Kingdom', '2025-03-05 14:15:00'),
('Diana', 'Prince', 'diana@example.com', 'United States', '2025-04-12 09:45:00'),
('Evan', 'Wright', 'evan@example.com', 'Australia', '2025-05-18 16:20:00');

INSERT INTO products (product_name, category, price, stock_quantity) VALUES
('Wireless Mouse', 'Electronics', 25.50, 120),
('Mechanical Keyboard', 'Electronics', 89.99, 45),
('Ergonomic Office Chair', 'Furniture', 249.99, 15),
('Hydro Water Bottle', 'Fitness', 34.95, 200),
('Noise Cancelling Headphones', 'Electronics', 199.99, 30);

INSERT INTO orders (user_id, order_date, status, total_amount) VALUES
(1, '2026-01-10 10:30:00', 'Shipped', 140.99),
(2, '2026-01-15 14:00:00', 'Shipped', 249.99),
(3, '2026-02-01 09:15:00', 'Delivered', 60.45),
(1, '2026-02-20 16:45:00', 'Delivered', 399.98),
(4, '2026-03-02 11:00:00', 'Processing', 34.95);

INSERT INTO order_items (order_id, product_id, quantity, unit_price) VALUES
(1, 1, 2, 25.50),  -- Alice bought 2 mice
(1, 2, 1, 89.99),  -- Alice bought 1 keyboard
(2, 3, 1, 249.99), -- Bob bought 1 chair
(3, 1, 1, 25.50),  -- Charlie bought 1 mouse
(3, 4, 1, 34.95),  -- Charlie bought 1 water bottle
(4, 5, 2, 199.99), -- Alice bought 2 headphones
(5, 4, 1, 34.95);  -- Diana bought 1 water bottle
ql
