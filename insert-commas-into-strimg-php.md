Solution:

```php
$string = 'HelloWorld';
$commaseparated = implode(',', strsplit($string));
echo $commaseparated; // Output: H,e,l,l,o,W,o,r,l,d
```

In this example, `strsplit($string)` breaks the string into an array of individual characters, and `implode(',', ...)` concatenates those characters with commas in between.

If the intention is to insert commas within each letter (e.g., 'H,e,l,l,o' instead of 'H,e,l,l,o,W,o,r,l,d'), a more complex approach would be needed, potentially involving character manipulation and looping. However, without further clarification or context, it's difficult to provide a more accurate solution.

Please note that this answer focuses on inserting commas between characters, not within letters. If the original query requires a different interpretation, please provide additional context or clarify the expected outcome.