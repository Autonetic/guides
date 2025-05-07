PHP MySQL PDO User Auth

To store user data and authentication methods, including Google OAuth, in separate tables using PHP and MySQL with PDO, you can follow a structured approach. This method ensures better security and flexibility in managing different authentication methods.

### Database Schema

1. **Users Table**:
   - Stores core user information.
   - **Columns**:
     - `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
     - `username` (VARCHAR)
     - `email` (VARCHAR)
     - `password` (VARCHAR) - For local authentication, store hashed passwords.

2. **Authentication Profiles Table**:
   - Stores authentication methods for each user.
   - **Columns**:
     - `user_id` (INT, FOREIGN KEY referencing `users.id`)
     - `service` (ENUM, e.g., 'website', 'google', 'facebook')
     - `token` (VARCHAR) - For OAuth tokens.

### Example Schema

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);

CREATE TABLE authentication_profiles (
    user_id INT,
    service ENUM('website', 'google', 'facebook') NOT NULL,
    token VARCHAR(255) NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### PHP Code Example

#### Step 1: Database Connection

```php
<?php
$host = 'localhost';
$dbname = 'your_database';
$username = 'your_username';
$password = 'your_password';

try {
    $pdo = new PDO("mysql:host=$host;dbname=$dbname", $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    die("Could not connect to the database $dbname :" . $e->getMessage());
}
?>
```

#### Step 2: Register a User with Local Authentication

```php
<?php
function registerUser($username, $email, $password) {
    global $pdo;

    $hashedPassword = password_hash($password, PASSWORD_DEFAULT);

    $stmt = $pdo->prepare('INSERT INTO users (username, email, password) VALUES (?, ?, ?)');
    $stmt->execute([$username, $email, $hashedPassword]);

    $userId = $pdo->lastInsertId();

    // Optionally, add a local authentication profile
    $stmt = $pdo->prepare('INSERT INTO authentication_profiles (user_id, service, token) VALUES (?, ?, ?)');
    $stmt->execute([$userId, 'website', $hashedPassword]);

    return $userId;
}
?>
```

#### Step 3: Register a User with Google OAuth

```php
<?php
function registerGoogleUser($email, $googleToken) {
    global $pdo;

    // Check if the user already exists
    $stmt = $pdo->prepare('SELECT * FROM users WHERE email = ?');
    $stmt->execute([$email]);
    $user = $stmt->fetch(PDO::FETCH_ASSOC);

    if (!$user) {
        // Create a new user
        $stmt = $pdo->prepare('INSERT INTO users (email) VALUES (?)');
        $stmt->execute([$email]);
        $userId = $pdo->lastInsertId();
    } else {
        $userId = $user['id'];
    }

    // Add or update the Google authentication profile
    $stmt = $pdo->prepare('INSERT INTO authentication_profiles (user_id, service, token) VALUES (?, ?, ?) ON DUPLICATE KEY UPDATE token = VALUES(token)');
    $stmt->execute([$userId, 'google', $googleToken]);

    return $userId;
}
?>
```

#### Step 4: Authenticate a User

```php
<?php
function authenticateUser($email, $password) {
    global $pdo;

    $stmt = $pdo->prepare('SELECT * FROM users WHERE email = ?');
    $stmt->execute([$email]);
    $user = $stmt->fetch(PDO::FETCH_ASSOC);

    if ($user && password_verify($password, $user['password'])) {
        session_start();
        $_SESSION['user_id'] = $user['id'];
        return true;
    }

    return false;
}
?>
```

#### Step 5: Authenticate a User with Google OAuth

```php
<?php
function authenticateGoogleUser($googleToken) {
    global $pdo;

    $stmt = $pdo->prepare('SELECT u.* FROM users u JOIN authentication_profiles ap ON u.id = ap.user_id WHERE ap.service = ? AND ap.token = ?');
    $stmt->execute(['google', $googleToken]);
    $user = $stmt->fetch(PDO::FETCH_ASSOC);

    if ($user) {
        session_start();
        $_SESSION['user_id'] = $user['id'];
        return true;
    }

    return false;
}
?>
```

### Summary

This approach ensures that user data and authentication methods are stored separately, enhancing security and flexibility. The `users` table stores core user information, while the `authentication_profiles` table manages different authentication methods, including local and OAuth-based logins. This separation allows for easy management and scalability of authentication methods.