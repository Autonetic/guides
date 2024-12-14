Simple PHP news class and database example. This example uses a MySQL database and PHP classes to manage news articles.

**Database Structure**

Create a MySQL table named `news` with the following columns:

* `id` (INT, PRIMARY KEY, AUTO_INCREMENT)
* `author` (VARCHAR)
* `content` (TEXT)
* `date` (DATE)

```sql
CREATE TABLE `news` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `author` varchar(255) DEFAULT NULL,
  `content` text DEFAULT NULL,
  `date` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
```


**News Class**

Create a PHP class `News` with the following methods:

* `__construct()` - Initializes the class with database connection settings
* `getNews()` - Retrieves all news articles from the database
* `addNews()` - Adds a new news article to the database
* `editNews()` - Updates an existing news article
* `deleteNews()` - Deletes a news article

Here's a simplified example implementation:
```php
class News {
    private $db;

    public function __construct($host, $username, $password, $database) {
        $this->db = new PDO("mysql:host=$host;dbname=$database", $username, $password);
    }

    public function getNews() {
        $query = "SELECT * FROM news";
        $stmt = $this->db->prepare($query);
        $stmt->execute();
        return $stmt->fetchAll(PDO::FETCH_ASSOC);
    }

    public function addNews($author, $content) {
        $query = "INSERT INTO news (author, content, date) VALUES (:author, :content, NOW())";
        $stmt = $this->db->prepare($query);
        $stmt->execute([':author' => $author, ':content' => $content]);
    }

    public function editNews($id, $author, $content) {
        $query = "UPDATE news SET author = :author, content = :content WHERE id = :id";
        $stmt = $this->db->prepare($query);
        $stmt->execute([':id' => $id, ':author' => $author, ':content' => $content]);
    }

    public function deleteNews($id) {
        $query = "DELETE FROM news WHERE id = :id";
        $stmt = $this->db->prepare($query);
        $stmt->execute([':id' => $id]);
    }
}
```
**Usage**

 Instantiate the `News` class with your database connection settings:
```php
$news = new News('localhost', 'username', 'password', 'database');
```
Then, you can use the class methods to interact with the news database:

* `getNews()` to retrieve all news articles
* `addNews()` to add a new news article
* `editNews()` to update an existing news article
* `deleteNews()` to delete a news article

For example:
```php
// Add a new news article
$news->addNews('John Doe', 'This is a new news article');

// Retrieve all news articles
$newsArticles = $news->getNews();
print_r($newsArticles);

// Edit an existing news article
$news->editNews(1, 'Jane Doe', 'Updated news article');

// Delete a news article
$news->deleteNews(2);
```
This is a basic example to demonstrate the concept. You may want to add additional features, such as user authentication, validation, and error handling, to make the news system more robust.
