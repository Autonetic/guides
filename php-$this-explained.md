In PHP object-oriented programming (OOP), `$this` is a special keyword that refers to the current object instance of a class. It is used to access the object's properties and methods from within the class itself.

When `$this` is Available

 `$this` is available when a method is called from within an object context. This means that when you're inside a class method, `$this` refers to the object that the method belongs to.

Example

Consider a simple class `Rectangle` with properties `width` and `height`, and methods `setSize`, `getArea`, and `getPerimeter`:
```php
class Rectangle {
    private $width;
    private $height;

    public function setSize($w, $h) {
        $this->width = $w;
        $this->height = $h;
    }

    public function getArea() {
        return $this->width  $this->height;
    }

    public function getPerimeter() {
        return 2  ($this->width + $this->height);
    }
}
```
When you create an instance of `Rectangle` and call its methods, `$this` refers to that object:
```php
$r = new Rectangle();
$r->setSize(4, 5);
echo $r->getArea(); // Output: 20
echo $r->getPerimeter(); // Output: 18
```
When `$this` is Not Available

 `$this` is not available when you call a method statically, i.e., without creating an object instance. In such cases, you cannot access object properties or methods using `$this`.

Best Practices

1. Use `$this` to access object properties and methods from within the class itself.
2. Avoid using `$this` in static methods, as it will not refer to an object instance.
3. Use `$this` consistently throughout your class to ensure clarity and maintainability.

Additional Resources

 PHP Manual: Variables from Outside the Class
 PHP Manual: Static Methods

By understanding the `$this` keyword, you'll be better equipped to write robust and maintainable PHP classes that leverage object-oriented programming principles.