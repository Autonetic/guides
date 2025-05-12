PHP Escape Pre Code Blocks

To escape all pre code blocks in PHP, you can use the `htmlspecialchars()` function to escape HTML characters within the `<pre>` tags. This function converts special characters to HTML entities, ensuring that the content is safely displayed without being interpreted as HTML markup.

Here is an example of how you can implement this:

```php
function escapePreformattedCode($html) {
    $doc = new DOMDocument();
    $doc->loadHTML($html);

    $pres = $doc->getElementsByTagName('pre');
    for ($i = 0; $i < $pres->length; $i += 1) {
        $node = $pres->item($i);

        $children = $node->childNodes;
        $content = '';
        for ($j = 0; $j < $children->length; $j += 1) {
            $child = $children->item($j);
            $content .= escapeRecursively($child);
        }
        $node->nodeValue = htmlspecialchars($content);
    }

    return $doc->saveHTML();
}

function escapeRecursively($node) {
    if ($node instanceof DOMText) {
        return $node->textContent;
    }
    $children = $node->childNodes;
    $content = "<{$node->nodeName}>";
    for ($i = 0; $i < $children->length; $i += 1) {
        $child = $children->item($i);
        $content .= escapeRecursively($child);
    }
    return "$content</{$node->nodeName}>";
}
```

This code uses the `DOMDocument` class to parse the HTML, find all `<pre>` tags, and escape their content using `htmlspecialchars()`. This ensures that any HTML special characters within the `<pre>` tags are properly escaped and displayed as text rather than interpreted as HTML markup

Additionally, it's important to note that the `htmlspecialchars()` function will escape the following characters: `&`, `<`, `>`, `"`, and `'`, which are commonly used in HTML and need to be escaped to prevent them from being interpreted as HTML tags or attributes

Using this method, you can safely display preformatted code blocks without worrying about HTML injection or other issues related to unescaped characters