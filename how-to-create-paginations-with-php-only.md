Display X Posts, Next Page

A simple PHP class that displays a specified number of posts (e.g., 10) and then allows navigation to the next page of posts. This implementation is not specific to WordPress or any other CMS.

**Post Paginator Class**
```php
class PostPaginator {
    private $postsPerPage = 10; // adjust this value to change the number of posts per page
    private $totalPosts;
    private $currentPage = 1;

    public function __construct(array $posts) {
        $this->totalPosts = count($posts);
    }

    public function getPosts(): array {
        $start = ($this->currentPage - 1) * $this->postsPerPage;
        $end = $start + $this->postsPerPage;
        $posts = array_slice(array_slice($this->posts, $start, $end), 0);
        return $posts;
    }

    public function hasPreviousPage(): bool {
        return $this->currentPage > 1;
    }

    public function hasNextPage(): bool {
        return $this->currentPage * $this->postsPerPage < $this->totalPosts;
    }

    public function previousPage(): void {
        $this->currentPage--;
    }

    public function nextPage(): void {
        $this->currentPage++;
    }
}
```
**Usage Example**

Assuming you have an array of posts `$allPosts` and you want to paginate them:
```php
$paginator = new PostPaginator($allPosts);

// Display the first page of posts
$posts = $paginator->getPosts();
foreach ($posts as $post) {
    echo $post['title'] . "\n";
}

// Navigation links
if ($paginator->hasPreviousPage()) {
    echo '<a href="#">Previous page</a>';
}
if ($paginator->hasNextPage()) {
    echo '<a href="#">Next page</a>';
}
```
**Notes**

1. This implementation assumes you have an array of posts with a simple structure (e.g., `array('title' => 'Post 1', 'content' => '...')`). You'll need to adapt the code to fit your specific post data format.
2. The `postsPerPage` property controls the number of posts displayed per page. Adjust this value as needed.
3. The `currentPage` property keeps track of the current page being displayed. You can use the `previousPage()` and `nextPage()` methods to navigate between pages.
4. This implementation does not handle pagination links or URL rewriting. You'll need to add your own logic to generate and handle these links.

Remember that this is a basic example, and you may want to add additional features, such as:

* Handling page numbers (e.g., displaying "Page 1 of 5")
* Implementing URL rewriting for pagination links
* Caching the paginated posts for performance
* Adding filters or sorting options

Feel free to ask if you have any questions or need further assistance!
