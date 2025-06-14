### This is a complete example of a PHP class that both handles file uploads and creates tokenized download links. In this demo, tokens are stored in an internal property (an array) so that you can see the flow end-to-end. In a production system you’d want to persist these tokens (for example, in a database) so that the download links survive beyond a single request cycle.

> **Note:** This demonstration code includes some basic file extension validation and security checks. Depending on your needs, you might extend the validations (e.g., check MIME types, maximum file size, or even use antivirus scanning) and also ensure that tokens are stored and managed securely.

---

```php
<?php
class TokenizedFileHandler {
    private $uploadDir;
    private $tokenLifetime; // lifetime in seconds
    // In-memory token storage for demonstration.
    // In a production system, store these in a database or a persistent cache.
    private $tokenStorage = [];

    /**
     * Constructor.
     *
     * @param string $uploadDir Directory where uploaded files will be stored.
     * @param int    $tokenLifetime Lifetime of download tokens in seconds (default 1 hour).
     */
    public function __construct($uploadDir = 'uploads/', $tokenLifetime = 3600) {
        $this->uploadDir = rtrim($uploadDir, '/') . '/';
        $this->tokenLifetime = $tokenLifetime;
        if (!is_dir($this->uploadDir)) {
            // Create upload directory with appropriate permissions.
            mkdir($this->uploadDir, 0755, true);
        }
    }

    /**
     * Securely handles the file upload.
     *
     * @param array $file The $_FILES['upload'] array.
     * @return string Returns the file path where the file is saved.
     * @throws Exception if the file upload fails or the file type is not allowed.
     */
    public function uploadFile($file) {
        // Check for basic file upload errors.
        if ($file['error'] !== UPLOAD_ERR_OK) {
            throw new Exception("Upload error code: " . $file['error']);
        }
        
        // Validate file extension. Customize this list based on acceptable file types.
        $allowedExtensions = ['jpg', 'jpeg', 'png', 'pdf', 'docx', 'txt'];
        $fileExt = strtolower(pathinfo($file['name'], PATHINFO_EXTENSION));
        if (!in_array($fileExt, $allowedExtensions)) {
            throw new Exception("File extension not allowed.");
        }
        
        // Generate a unique file name to avoid collisions.
        $uniqueName = uniqid('', true) . '.' . $fileExt;
        $destination = $this->uploadDir . $uniqueName;
        
        if (!move_uploaded_file($file['tmp_name'], $destination)) {
            throw new Exception("Failed to move uploaded file.");
        }
        
        // Return the full path to the uploaded file.
        return $destination;
    }

    /**
     * Generates a secure, tokenized download link for an uploaded file.
     *
     * @param string $filePath Path to the uploaded file.
     * @return string Returns the generated token.
     * @throws Exception if the file does not exist.
     */
    public function generateToken($filePath) {
        if (!file_exists($filePath)) {
            throw new Exception("File does not exist.");
        }
        
        // Generate a cryptographically secure token (32 hex characters).
        $token = bin2hex(random_bytes(16));
        $expiry = time() + $this->tokenLifetime;
        
        // Store token details. In production, persist these details.
        $this->tokenStorage[$token] = [
            'file' => $filePath,
            'expiry' => $expiry
        ];
        
        return $token;
    }
    
    /**
     * Validates a token and returns the associated file path if valid.
     *
     * @param string $token Token to be validated.
     * @return string Returns the path to the file.
     * @throws Exception if the token is invalid or expired.
     */
    private function validateToken($token) {
        if (!isset($this->tokenStorage[$token])) {
            throw new Exception("Invalid token.");
        }
        
        $data = $this->tokenStorage[$token];
        if (time() > $data['expiry']) {
            // Remove expired token.
            unset($this->tokenStorage[$token]);
            throw new Exception("Token expired.");
        }
        
        return $data['file'];
    }

    /**
     * Handles a token-based download.
     * This method sends appropriate headers and streams the file content.
     *
     * @param string $token The token provided in the download request.
     * @return void
     * @throws Exception if the token or file is invalid.
     */
    public function handleDownload($token) {
        $filePath = $this->validateToken($token);

        if (!file_exists($filePath)) {
            throw new Exception("File not found.");
        }

        // Optionally, for one-time downloads, you might remove the token here:
        // unset($this->tokenStorage[$token]);

        // Get the MIME type and set headers.
        $mimeType = mime_content_type($filePath);
        header('Content-Description: File Transfer');
        header('Content-Type: ' . $mimeType);
        header('Content-Disposition: attachment; filename="' . basename($filePath) . '"');
        header('Expires: 0');
        header('Cache-Control: must-revalidate');
        header('Pragma: public');
        header('Content-Length: ' . filesize($filePath));
        readfile($filePath);
        exit;
    }

    /**
     * Returns a URL that embeds the token for file downloading.
     *
     * @param string $token The token associated with the file.
     * @return string Returns the public URL for downloading.
     */
    public function getDownloadLink($token) {
        // This assumes you have a separate download script (e.g., download.php)
        // that accepts a token query parameter.
        return "download.php?token=" . urlencode($token);
    }
}
?>
```

---

### Usage Example

Below is an example of how you might use this class in your application. The first part demonstrates a file upload handling with token generation. Save this code in (for example) `upload.php`:

```php
<?php
require_once 'TokenizedFileHandler.php';

$fileHandler = new TokenizedFileHandler();

if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_FILES['upload'])) {
    try {
        // Upload the file and get its stored path.
        $uploadedFilePath = $fileHandler->uploadFile($_FILES['upload']);
        echo "File uploaded to: " . htmlspecialchars($uploadedFilePath) . "<br>";

        // Generate a download token.
        $token = $fileHandler->generateToken($uploadedFilePath);
        $downloadLink = $fileHandler->getDownloadLink($token);
        echo "Download your file using this link: <a href='" . htmlspecialchars($downloadLink) . "'>" . htmlspecialchars($downloadLink) . "</a>";
    } catch (Exception $e) {
        echo "Error: " . $e->getMessage();
    }
}
?>
```

And here’s a simple download script (save it as `download.php`) that uses the token to serve the file:

```php
<?php
require_once 'TokenizedFileHandler.php';

// Instantiate the handler. Note: in a persistent setup, you would retrieve token data from your persistent storage.
$fileHandler = new TokenizedFileHandler();

if (!isset($_GET['token'])) {
    echo "Token missing.";
    exit;
}

try {
    $fileHandler->handleDownload($_GET['token']);
} catch (Exception $ex) {
    echo "Error: " . $ex->getMessage();
}
?>
```

---

### Further Thoughts

- **Persistent Storage:**  
  For a real-world application, comment out the in-memory token storage and replace it with a database (using PDO/MySQL, for example) to store and manage tokens over multiple requests. This ensures that tokens remain valid even if the process handling the upload is restarted.

- **Enhanced Validation:**  
  You can expand validation to include file size checks, MIME type verification, and even virus scanning if your application demands higher security.

- **One-Time Tokens:**  
  You may want to remove the token immediately after a successful download so that the same link cannot be reused. This is as simple as uncommenting the line with `unset($this->tokenStorage[$token]);` in the `handleDownload()` method.

- **URL Generation for Production:**  
  The `getDownloadLink()` method currently returns a hard-coded relative URL. In a production environment, consider dynamically generating URLs (possibly including hostnames, HTTPS enforcement, etc.) to ensure security and correctness.

This modular design should work well within a PHP application focused on security and simplicity. Feel free to ask about integrating token data with a database or other advanced enhancements for managing file access securely and at scale![43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/mysqldumper/mysqldumper/tree/1552331407d59c3a629c7e21ff71455f7a48c5f7/include%2Fcore.php?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "1")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/vatandoost/filemanager/tree/4c04f1a0efba4b5a414a61d8b5a07ae0a387bca2/Utils.php?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "2")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/edicom2019/erp/tree/a50e604aaf4489b4a5e424b6ec16be6a04bccf06/modules%2FSettings%2FMail%2Factions%2FDownloadAttachment.php?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "3")[43dcd9a7-70db-4a1f-b0ae-981daa162054](https://github.com/niceit/sf-tracker/tree/1e7e08838648ff7b1b0245ae4a43bad5566496e7/sf-tracker%2Fsrc%2FTrackersBundle%2FController%2FDownloadController.php?citationMarker=43dcd9a7-70db-4a1f-b0ae-981daa162054 "4")