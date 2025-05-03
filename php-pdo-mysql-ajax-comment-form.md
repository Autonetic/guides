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

2. **PHP Form**: index.php - Create a PHP form that allows users to input their name, email, comment, and submit the form. The form should also include a hidden field for `post_id`. this code presumes that your page request will include the id of the post that the comment belongs to. For example `http://localhost/blog/index.php?id=1`. For testing purposes, I have made default requests belong to post id `0`. 

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

3. **AJAX Request**: Use JavaScript (jQuery) to handle the form submission via AJAX. This will send the form data to a PHP script without refreshing the page.

   ```javascript
   $(document).ready(function() {
       $('#commentForm').on('submit', function(event) {
           event.preventDefault();
           var formData = $(this).serialize();
           $.ajax({
               url: "submit_comment.php",
               method: "POST",
               data: formData,
               dataType: "JSON",
               success: function(response) {
                   if (!response.error) {
                       $('#commentForm')reset();
                       $('#comment_id').val('0');
                       $('#message').html(response.message);
                       showComments();
                   } else if (response.error) {
                       $('#message').html(response.message);
                   }
               }
           });
       });
   });
   ```

4. **PHP Script**: Create a PHP script (`submit_comment.php`) that uses PDO to insert the comment into the database.

   ```php
   <?php
   if (!empty($_POST["name"]) && !empty($_POST["email"]) && !empty($_POST["comment"])) {
       $post_id = $_POST["post_id"];
       $comment_id = $_POST["comment_id"];
       $name = $_POST["name"];
       $email = $_POST["email"];
       $comment = $_POST["comment"];
       $submit_date = date("Y-m-d H:i:s");

       try {
           $db_host = "localhost";
           $db_user = "root";
           $db_pass = "pass";
           $db_name = "your_database";
           $db_conn = new PDO("mysql:host={$db_host};dbname={$db_name}", $db_user, $db_pass);
           $db_conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

           $stmt = $db_conn->prepare("INSERT INTO comments (post_id, comment_id, name, email, comment, submit_date) VALUES (?, ?, ?, ?, ?, ?)");
           $stmt->execute([$post_id, $comment_id, $name, $email, $comment, $submit_date]);

           $message = "Comment posted successfully.";
           $status = array('error' => 0, 'message' => $message);
       } catch (PDOException $e) {
           $message = "Error: Comment not posted.";
           $status = array('error' => 1, 'message' => $message);
       }

       echo json_encode($status);
   } else {
       echo json_encode(array('error' => 1, 'message' => 'All fields are required.'));
   }
   ?>
   ```

5. **Display Comments**: Create a function to display the comments from the database using AJAX.

   ```javascript
   function showComments() {
       $.ajax({
           url: "show_comments.php",
           method: "GET",
           success: function(data) {
               $('#comments').html(data);
           }
       });
   }
   ```

6. **PHP Script for Comments Display**: Create a PHP script (`show_comments.php`) that retrieves and displays the comments from the database.

   ```php
   <?php
   if (isset($_GET['post_id'])) {
       $post_id = $_GET['post_id'];
       try {
           $db_host = "localhost";
           $db_user = "root";
           $db_pass = "pass";
           $db_name = "your_database";
           $db_conn = new PDO("mysql:host={$db_host};dbname={$db_name}", $db_user, $db_pass);
           $db_conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

           $stmt = $db_conn->prepare("SELECT * FROM comments WHERE post_id = ? ORDER BY submit_date DESC");
           $stmt->execute([$post_id]);
           $comments = $stmt->fetchAll(PDO::FETCH_ASSOC);

           foreach ($comments as $comment) {
               echo "<div>";
               echo "<strong>" . htmlspecialchars($comment['name']) . "</strong> on " . htmlspecialchars($comment['submit_date']) . "<br>";
               echo htmlspecialchars($comment['comment']) . "<br>";
               echo "</div>";
           }
       } catch (PDOException $e) {
           echo "Error: " . $e->getMessage();
       }
   } else {
       echo "No post ID specified!";
   }
   ?>
   ```

By following these steps, you can create a PHP PDO MySQL AJAX comment form that includes fields for name, email, date, comment_id, and post_id. 
