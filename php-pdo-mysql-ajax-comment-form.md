Working PHP, PDO, MySQL and AJAX Comment Form.

I have made a very simple php/ajax comment form, I ran into a series of shorfalls along the way, so I have written this guide, to both help me remember AJAX calls and to help anyone else who may be having similar issues when tryng to get PHP to talk with jQuery.

To create a PHP PDO MySQL AJAX comment form that includes fields for name, email, date, comment_id, and post_id, you can follow these steps (I will provide the files at the end of the guide):

1. **Database Table Creation**: First, create a MySQL table to store the comments. This table should include columns for `comment_id`, `post_id`, `timestamp`, `name`, `email` and `message`.

```sql
CREATE TABLE `comments` (
  `comment_id` int(11) NOT NULL AUTO_INCREMENT,
  `post_id` bigint(20) NOT NULL,
  `timestamp` datetime NOT NULL DEFAULT current_timestamp(),
  `name` varchar(255) NOT NULL,
  `email` varchar(255) NOT NULL,
  `message` text NOT NULL,
  PRIMARY KEY (`comment_id`)
) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;
```

2. **PHP Form**: (`index.php`) Create a PHP form that allows users to input their name, email, comment, and submit the form. The form should also include a hidden field for `post_id`. this code presumes that your page request will include the id of the post that the comment belongs to. For example `http://localhost/blog/index.php?id=1`. For testing purposes, I have made default requests belong to post id `0`. 

```php
<?php
if(!isset($_GET['id'])) {
    $_GET['id'] = '0';
}
?>
<head>
<title>PHP AJAX Comments</title>
<script src="https://code.jquery.com/jquery-latest.min.js"></script>
</head>
<body>
<div id="comment_message"></div>

<form id="comment_form">
    <input type="text" name="name" placeholder="Name" required>
    <input type="text" name="email" placeholder="Email" required>
    <textarea name="message" placeholder="Comment" required></textarea>
    <input type="hidden" name="post_id" value="<?php $_GET['id'];?>">
    <button type="submit">Submit</button>
</form>
<div id="display_comment"></div>

<script>
$(document).ready(function(){
    $('#comment_form').on('submit', function(event){
        event.preventDefault();
        var form_data = $(this).serialize();
        $.ajax({
            url:"comment-add.php",
            method:"POST",
            data:form_data,
            dataType:"JSON",
            /*success:function(data) {
                if(data.error != '') {
                    $('#comment_form').reset();
                    $('#comment_message').html(data.error);
                    $('#comment_id').val('0');
                    load_comment();
                }*/
            success: function(response) {
                if (response.error == 0) {
                    $('#comment_message').html(response.message);
                    setTimeout(function() {
                        // Your jQuery action here
                        location.reload();
                    }, 1000); // Delay in millisecondslocation.reload();
                    $('#comment_form').reset();
                    $('#comment_id').val('0');
                    load_comment();
                } else if (response.error) {
                    $('#comment_message').html(response.message);
                }
            }
        })
    });
    load_comment();
    function load_comment() {
        $.ajax({
            url:"comment-list.php",
            method:"POST",
            success:function(data) {
                $('#display_comment').html(data);
            }
        })
    }
});
</script>
```

3. **PHP Script**: (`db.php`) Create a database connection file, we'll use PDO for this.
```php
<?php
//database credentials
define('DBHOST','localhost');
define('DBUSER','comments');
define('DBPASS','#1234567');
define('DBNAME','comments_test');

$pdo = new PDO("mysql:host=".DBHOST.";port=8889;dbname=".DBNAME, DBUSER, DBPASS);
$pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
?>
```


4. **PHP Script**: Create a PHP script (`comment-add.php`) that uses PDO to insert the comment into the database.

```php
<?php
include 'db.php'; // Database connection file
date_default_timezone_set('Australia/Melbourne');
if(!isset($_GET['id'])) {
    $_GET['id'] = '0';
}
if(isset($_POST['name'], $_POST['email'], $_POST['message']) && filter_var($_POST['email'], FILTER_VALIDATE_EMAIL)) {
    $post_id = $_GET['id'] ?? 0;
    $timestamp = date('Y-m-d H:i:s', time());
    $name = $_POST['name'];
    $email = $_POST['email'];
    $message = $_POST['message'];
    $sql = "INSERT INTO comments (post_id, timestamp, name, email, message) VALUES (?, ?, ?, ?, ?)";
    $stmt = $pdo->prepare($sql);
    $stmt->execute([$post_id, $timestamp, $name, $email, $message]);
    $comment_message = "Comment posted";
    $status = array('error' => 0, 'message' => $comment_message);
    echo json_encode($status);
} elseif(isset($_POST['name'], $_POST['email'], $_POST['message']) && !filter_var($_POST['email'], FILTER_VALIDATE_EMAIL)) {
    $comment_message = "Invalid email address";
    $status = array('error' => 1, 'message' => $comment_message);
    echo json_encode($status);
} else {
    $comment_message = "Invalid input";
    $status = array('error' => 1, 'message' => $comment_message);
    echo json_encode($status);
}
?>
```

5. **Display Comments**: (`comment-list.php`) Create another PHP file that will get any comments that already exist.

```php
<?php
include 'db.php'; // Database connection file
$sql = "SELECT * FROM comments ORDER BY comment_id DESC";
$stmt = $pdo->query($sql);
$comments = $stmt->fetchAll(PDO::FETCH_ASSOC);
foreach ($comments as $comment) {
    echo "<div>";
    echo "<p><strong>" . htmlspecialchars($comment['name']) . "</strong>: " . htmlspecialchars($comment['message']) . "</p>";
    echo "<p><small>Posted at: " . $comment['timestamp'] . "</small></p>";
    if ($comment['post_id'] == 0) {
        echo "<button onclick='postReply(" . $comment['comment_id'] . ")'>Reply</button>";
    }
    echo "</div>";
}
if (empty($comments)) {
    echo "<div>";
    echo "<p><strong>" . "No Comments Yet." . "</strong></p>";
    echo "</div>";
}
?>
 ```

Thats pretty much it, following these steps you will be able to create a PHP, PDO, MySQL and AJAX comment form that includes a little email validation! =)

All source files available at [Autonetix.au](https://autonetix.au)
