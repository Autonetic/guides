PHP PDO MySQL AJAX Comment Form

To create a PHP PDO MySQL AJAX comment form that includes fields for name, email, date, comment_id, and post_id, you can follow these steps:

1. **Database Table Creation**: First, create a MySQL table to store the comments. This table should include columns for `id`, `post_id`, `comment_id`, `name`, `email`, `comment`, and `submit_date`.

   ```sql
   CREATE TABLE comments (
       id INT AUTO_INCREMENT PRIMARY KEY,
       post_id INT NOT NULL,
       comment_id INT DEFAULT 0,
       name VARCHAR(255) NOT NULL,
       email VARCHAR(255) NOT NULL,
       comment TEXT NOT NULL,
       submit_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );
   ```

2. **HTML Form**: Create an HTML form that allows users to input their name, email, comment, and submit the form. The form should also include hidden fields for `post_id` and `comment_id`.

   ```html
   <form id="commentForm">
       <input type="hidden" id="post_id" name="post_id" value="123">
       <input type="hidden" id="comment_id" name="comment_id" value="0">
       <input type="text" id="name" name="name" placeholder="Name" required>
       <input type="email" id="email" name="email" placeholder="Email" required>
       <textarea id="comment" name="comment" placeholder="Comment" required></textarea>
       <button type="submit">Submit Comment</button>
   </form>
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