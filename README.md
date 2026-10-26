# CertiKeep

**A Web-Based Personal Document Expiration & Renewal Management System**

CertiKeep is a lightweight web application that helps individuals track the lifecycles and renewal timelines of important personal records. With automated date calculations, visual status indicators, and categorized management, it helps you avoid penalties and hassle caused by expired IDs, warranties, vehicle registrations, and recurring subscriptions.

## Table of Contents

- [Features](#features)
- [Technology Stack](#technology-stack)
- [Database Schema](#database-schema)
- [Project Structure](#project-structure)
- [Installation & Setup](#installation--setup)
- [Core Logic](#core-logic)
- [License](#license)

## Features

- **Real-time expiration tracking:** Remaining validity is calculated from the system date, and each record is flagged as *Active*, *Expiring Soon*, or *Expired*.
- **Color-coded dashboard:** Status badges give immediate visual feedback.
- **Full CRUD:** Create, read, update, and delete document records.
- **Category organization:** Group entries such as Government IDs, Subscriptions, Warranties, and Vehicle Documents.
- **Urgency-based sorting:** Records are sorted by expiration date so the most urgent renewals appear first.

## Technology Stack

| Layer    | Technology                                   |
|----------|----------------------------------------------|
| Frontend | HTML5, CSS3 (custom responsive layout)       |
| Backend  | PHP 8.x (PDO with prepared statements)       |
| Database | MySQL / MariaDB                              |
| Server   | Apache (XAMPP / WAMP / Laragon)              |

## Database Schema

Run the following script in phpMyAdmin or any MySQL client:

```sql
CREATE DATABASE IF NOT EXISTS certikeep_db;
USE certikeep_db;

CREATE TABLE IF NOT EXISTS documents (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(120) NOT NULL,
    category VARCHAR(50) NOT NULL,
    expiration_date DATE NOT NULL,
    renewal_cost DECIMAL(10,2) DEFAULT NULL,
    notes TEXT DEFAULT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## Project Structure

```
certikeep/
├── config/
│   └── db.php           # PDO database connection configuration
├── assets/
│   └── css/
│       └── style.css    # Responsive styles and color-coded status badges
├── includes/
│   ├── header.php       # Shared navigation layout and meta tags
│   └── footer.php       # Global footer elements
├── add-document.php     # Form for creating new entries
├── edit.php             # Script and interface for updating records
├── delete.php           # Record deletion logic
├── index.php            # Main management dashboard
└── README.md            # System documentation
```

## Installation & Setup

### Prerequisites

- PHP 8.0 or higher
- MySQL or MariaDB
- Apache via XAMPP, WAMP, or Laragon

### Steps

1. **Copy the project** into your web server's root folder:
   - XAMPP: `C:\xampp\htdocs\certikeep`
   - WAMP: `C:\wamp64\www\certikeep`
   - Laragon: `C:\laragon\www\certikeep`

2. **Start the services.** Launch Apache and MySQL from your control panel.

3. **Create the database.** Open `http://localhost/phpmyadmin`, go to the **SQL** tab, and run the script from the [Database Schema](#database-schema) section.

4. **Configure the connection.** Open `config/db.php` and confirm the credentials match your environment:

   ```php
   $host = 'localhost';
   $db   = 'certikeep_db';
   $user = 'root';
   $pass = '';   // default for XAMPP
   ```

5. **Run the app.** Visit `http://localhost/certikeep/` in your browser.

## Core Logic

**Status calculation.** On each page load, `index.php` compares every record's `expiration_date` with the current date:

| Status            | Condition                                              | Badge  |
|-------------------|--------------------------------------------------------|--------|
| **Expired**       | Expiration date is before today                        | Red    |
| **Expiring Soon** | Expires within the warning window (30 days by default) | Yellow |
| **Active**        | Expires after the warning window                       | Green  |

**Sorting.** Records are fetched with `ORDER BY expiration_date ASC`, so expired and soon-to-expire documents appear at the top.

**Security.** All database operations use PDO prepared statements to protect against SQL injection, and output should be escaped with `htmlspecialchars()` to prevent XSS.

