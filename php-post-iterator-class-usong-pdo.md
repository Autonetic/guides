Retrieving 10 Posts with PDO

To retrieve 10 posts at a time using PHP and PDO, you can use a combination of prepared statements and LIMIT clause. Here's an example:

```php
<?php

// Assume you have a PDO connection object $pdo

class PostIterator {
    private $pdo;
    private $limit;
    private $offset;

    public function __construct(PDO $pdo, int $limit = 10) {
        $this->pdo = $pdo;
        $this->limit = $limit;
        $this->offset = 0;
    }

    public function current(): array {
        $stmt = $this->pdo->prepare("SELECT * FROM posts LIMIT :limit OFFSET :offset");
        $stmt->bindParam(':limit', $this->limit);
        $stmt->bindParam(':offset', $this->offset);
        $stmt->execute();
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }

    public function next(): void {
        $this->offset += $this->limit;
    }

    public function valid(): bool {
        return $this->offset < (int) $this->pdo->query("SELECT COUNT(*) FROM posts")->fetchColumn();
    }

    public function rewind(): void {
        $this->offset = 0;
    }
}

// Usage
$iterator = new PostIterator($pdo);
while ($iterator->valid()) {
    $posts = $iterator->current();
    // Process the retrieved posts
    $iterator->next();
}
```

In this example, the `PostIterator` class uses a prepared statement with LIMIT and OFFSET clauses to retrieve 10 posts at a time. The `current()` method executes the query and returns the retrieved posts as an array. The `next()` method increments the offset to retrieve the next batch of posts. The `valid()` method checks if there are more posts to retrieve, and the `rewind()` method resets the offset to retrieve the first batch again.

Note that you should adjust the SQL query to match your actual table and column names. Also, make sure to use prepared statements to prevent SQL injection attacks.

You can also use a foreach loop instead of a while loop to iterate over the posts:

```php
foreach ($iterator as $post) {
    // Process the retrieved post
}
```

This will automatically call `current()`, `next()`, and `valid()` methods for you.