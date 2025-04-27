To store user data and authentication methods, including Google OAuth, in separate tables using PHP and MySQL with PDO, you can follow these steps:

1. **Create a User Table**: This table will store basic user information.
   ```sql
   CREATE TABLE users (
       id INT AUTO_INCREMENT PRIMARY KEY,
       username VARCHAR(255) NOT NULL,
       email VARCHAR(255) NOT NULL,
       password VARCHAR(255) -- Only used for local authentication
   );
   ```

2. **Create an Authentication Profile Table**: This table will store authentication methods and tokens.
   ```sql
   CREATE TABLE auth_profiles (
       id INT AUTO_INCREMENT PRIMARY KEY,
       user_id INT,
       service ENUM('website', 'google', 'facebook') NOT NULL,
       token VARCHAR(255),
       FOREIGN KEY (user_id) REFERENCES users(id)
   );
   ```

3. **Example PHP Code for Google OAuth Login**:
   - Connect to the database.
   - Authenticate the user with Google OAuth.
   - Check if the user exists in the database.
   - If the user does not exist, insert the user into the `users` table.
   - Insert the authentication method into the `auth_profiles` table.
   ```php
   // Database connection
   $dsn = 'mysql:host=localhost;dbname=your_database';
   $username = 'your_username';
   $password = 'your_password';
   $options = [
       PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
       PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
       PDO::MYSQL_ATTR_SSL_CA => $server_cert_file_path,
       PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT => false
   ];

   try {
       $pdo = new PDO($dsn, $username, $password, $options);
   } catch (PDOException $e) {
       echo 'Connection failed: ' . $e->getMessage();
   }

   // Google OAuth authentication
   if (isset($profile['email'])) {
       $stmt = $pdo->prepare('SELECT * FROM users WHERE email = ?');
       $stmt->execute([$profile['email']]);
       $account = $stmt->fetch();

       if (!$account) {
           $stmt = $pdo->prepare('INSERT INTO users (email, name) VALUES (?, ?)');
           $stmt->execute([$profile['email'], $profile['name']]);
           $user_id = $pdo->lastInsertId();
       } else {
           $user_id = $account['id'];
       }

       $stmt = $pdo->prepare('INSERT INTO auth_profiles (user_id, service, token) VALUES (?, ?, ?)');
       $stmt->execute([$user_id, 'google', $profile['token']]);
   }
   ```

This setup allows for a clean separation of user data and authentication methods, making it easier to manage different authentication sources like Google OAuth, Facebook, or local accounts. Additionally, it helps in securing user data by isolating sensitive information and authentication tokens.

Separating the user data from the authentication methods into different tables can enhance security and maintainability of the application