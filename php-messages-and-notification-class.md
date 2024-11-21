PHP MySQL Notification Class

This class can store and retrieve user notifications in MySQL. It can store in a MySQL database table user status notifications. The status includes the subject, the actor user involved in the notification, and the status indicating if the user has seen the notification already or not.

**Class Methods**
-----------------

### `saveNotification()`

*   Stores a new notification in the MySQL database table.
*   Parameters:
    *   `$title`: The title of the notification.
    *   `$message`: The message of the notification.
    *   `$ntime`: The time the notification was created.
    *   `$repeat`: The repeat status of the notification.
    *   `$nloop`: The loop status of the notification.
    *   `$username`: The username of the user who created the notification.

### `getNotificationByUser()`

*   Retrieves the last unseen notifications for a given user.
*   Parameters:
    *   `$username`: The username of the user.

### `updateNotification()`

*   Updates the status of a notification to seen.
*   Parameters:
    *   `$notification_id`: The ID of the notification.

**Example Usage**
-----------------

```php
$notification = new Notification();
$notification->saveNotification('New News', 'This is a new news notification.', '2022-01-01 12:00:00', 0, 0, 'admin');
$notifications = $notification->getNotificationByUser('admin');
foreach ($notifications as $notification) {
    echo $notification['title'] . '<br>';
    echo $notification['message'] . '<br>';
    $notification->updateNotification($notification['id']);
}
```

**Database Schema**
-------------------

```sql
CREATE TABLE `notifications` (
    `id` INT AUTO_INCREMENT PRIMARY KEY,
    `title` VARCHAR(250) NOT NULL,
    `message` TEXT NOT NULL,
    `ntime` DATETIME NOT NULL,
    `repeat` TINYINT(1) NOT NULL DEFAULT 0,
    `nloop` TINYINT(1) NOT NULL DEFAULT 0,
    `username` VARCHAR(100) NOT NULL,
    `seen` TINYINT(1) NOT NULL DEFAULT 0
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

**Notification Class**
---------------------

```php
class Notification {
    private $conn;
    private $notificationTable;

    public function __construct($conn, $notificationTable) {
        $this->conn = $conn;
        $this->notificationTable = $notificationTable;
    }

    public function saveNotification($title, $message, $ntime, $repeat, $nloop, $username) {
        $insertQuery = "INSERT INTO " . $this->notificationTable . " (title, message, ntime, repeat, nloop, username) VALUES (?, ?, ?, ?, ?, ?)";
        $stmt = $this->conn->prepare($insertQuery);
        $stmt->bind_param("sssiis", $title, $message, $ntime, $repeat, $nloop, $username);
        if ($stmt->execute()) {
            return true;
        }
        return false;
    }

    public function getNotificationByUser($username) {
        $query = "SELECT * FROM " . $this->notificationTable . " WHERE username = ? AND seen = 0";
        $stmt = $this->conn->prepare($query);
        $stmt->bind_param("s", $username);
        $stmt->execute();
        $result = $stmt->get_result();
        $notifications = array();
        while ($row = $result->fetch_assoc()) {
            $notifications[] = $row;
        }
        return $notifications;
    }

    public function updateNotification($notification_id) {
        $updateQuery = "UPDATE " . $this->notificationTable . " SET seen = 1 WHERE id = ?";
        $stmt = $this->conn->prepare($updateQuery);
        $stmt->bind_param("i", $notification_id);
        if ($stmt->execute()) {
            return true;
        }
        return false;
    }
}
```