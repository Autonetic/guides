# How to use enums in php 8.2 in 2024
Hey there, a quick example of the best way to store static data in a mysql database in 2024.

The best practice is to create a class in php so that these rank variables stay the same both in the database and in your code.

example:

```php
class Admin {

    public static function get_rank()
    {

        // only works in php >=5.4 otherwise use return array('Category1'...);
        return [
          'Administrator' => 'Admin',
          'Moderator' => 'Mod',
          'User' => 'User'
        ];
    }

}
```
