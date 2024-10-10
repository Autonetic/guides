# PHP Quick Tutorial - Helper functions.
### Helper functions to return odd or even int's

Welcome to this quick guide for PHP programming!

We will be covering how to make a simple PHP helper function that returns even or odd int's. I have found this function usefull in some of my projects, and thought I would share it.

## Code
- Firstly, create a new php file, or add this function to your existing code:

```php
function is_even($int) {
    if ($int % 2 == 0 ) {
        return true;
    } else {
        return false;
    }
}
```
- Usage is simple, if the int supplied to the function is even, the function returns true, and false for odd.
- Modify freely if needed =).
