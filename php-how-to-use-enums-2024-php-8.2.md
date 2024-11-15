# How to use enums in php 8.2 in 2024
Hey there, a quick example of the best way to store static data in a mysql database in 2024.

The best practice is to create a class in php so that these rank variables stay the same both in the database and in your code.

example:

```php
class Admin {

    public static function get_rank()
    {

        // only works in php >=5.4 otherwise use return array('Administrator'...);
        return [
          'Administrator' => 'Admin',
          'Moderator' => 'Mod',
          'User' => 'User'
        ];
    }

}
```
**simple usage:**
`Admin::get_rank()`

## MySQL ENUM Example
here are some examples for processing data on the database end:

```sql
CREATE TABLE ranks(
    id INT PRIMARY KEY AUTO_INCREMENT,
    ranks ENUM('Administrator', 'Moderator', 'User') NOT NULL
);
```

# Using ENUM specifically:
instead of the above methods, you could use php's built in enum or enumeration functions:

```php
enum Rank
{
    case ADMIN;
    case MOD;
    case USER;
    
    public function ranks(): string
    {
        return match($this) 
        {
            Rank::ADMIN => 'Administrator',   
            Rank::MOD => 'Moderator',   
            Rank::USER => 'User',   
        };
    }
}
```
usage would then be:

```php
$rank = Rank::ADMIN;

$rank->ranks(); // 'Administrator'
```

In context:

```php
$rank = Rank::ADMIN;
//Using $rank->ranks();
if ($rank === Rank::ADMIN) {
  // Handle admin-specific logic
  echo "Hello " . $rank->ranks();
} elseif ($rank === Rank::MOD) {
  // Handle moderator-specific logic
  echo "Hello " . $rank->ranks();
} else {
  // Handle user-specific logic
  echo "Welcome " . $rank->ranks();
}
```


# the following needs to be reviewed:

Enums are a new feature in PHP 8.1 that allows you to define a custom type with a fixed set of values. In the context of MySQL and PHP, enums can be used to represent a limited set of values, such as ranks, in a more type-safe and readable way.

**MySQL ENUM Type**

In MySQL, the ENUM type is a string object that can have a value chosen from a list of permitted values specified at table creation time. For example:
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  rank ENUM('admin', 'moderator', 'user')
);
```
This creates a `rank` column with three possible values: `admin`, `moderator`, and `user`.

**PHP 8.1 Enums**

In PHP 8.1, you can define an enum type using the `enum` keyword:
```php
enum Rank {
  case ADMIN;
  case MODERATOR;
  case USER;
}
```
This defines an `Rank` enum with three possible values: `ADMIN`, `MODERATOR`, and `USER`.

**Mapping MySQL ENUM to PHP 8.1 Enum**

To map the MySQL ENUM values to the PHP 8.1 enum, you can use a simple mapping array:
```php
$rankMap = [
  'admin' => Rank::ADMIN,
  'moderator' => Rank::MODERATOR,
  'user' => Rank::USER,
];
```
This mapping array translates the MySQL ENUM values to their corresponding PHP 8.1 enum values.

**Using Enums in PHP**

Now that you have defined the enum and mapped it to the MySQL ENUM values, you can use it in your PHP code:
```php
$user = new User();
$user->rank = Rank::MODERATOR; // Set the rank using the enum

// Retrieve the rank from the database
$rank = $user->getRank(); // Returns the enum value (e.g., Rank::MODERATOR)

// Validate the rank
if ($rank === Rank::ADMIN) {
  // Handle admin-specific logic
} elseif ($rank === Rank::MODERATOR) {
  // Handle moderator-specific logic
} else {
  // Handle user-specific logic
}
```
**Benefits**

Using enums in PHP for ranks with MySQL provides several benefits:

1. **Type safety**: Enums ensure that only valid values are assigned to the `rank` column, preventing invalid or unexpected values from being stored.
2. **Readability**: Enums make your code more readable by providing a clear and concise way to represent the possible values.
3. **Maintainability**: Enums simplify the process of adding or removing values from the `rank` column, as you only need to update the mapping array.

By leveraging enums in PHP 8.1 and mapping them to MySQL ENUM values, you can create a more robust and maintainable system for representing ranks in your application.
