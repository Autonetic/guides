PHP List Files and Types

Asimple PHP class that lists files and filetypes from a selected directory.

```php
class FileLister {
    private $directory;

    public function __construct($directory) {
        $this->directory = $directory;
    }

    public function listFiles() {
        $files = array();
        $iterator = new FilesystemIterator($this->directory);
        foreach ($iterator as $entry) {
            if ($entry->isFile()) {
                $files[] = array(
                    'filename' => $entry->getFilename(),
                    'filetype' => $entry->getExtension(),
                    'filepath' => $entry->getPathname()
                );
            }
        }
        return $files;
    }
}
```

**Example Usage**
---------------

```php
$directory = '/path/to/your/directory';
$fileLister = new FileLister($directory);
$files = $fileLister->listFiles();

foreach ($files as $file) {
    echo "Filename: " . $file['filename'] . "\n";
    echo "Filetype: " . $file['filetype'] . "\n";
    echo "Filepath: " . $file['filepath'] . "\n\n";
}
```

**How it Works**
----------------

1. The `FileLister` class takes a directory path in its constructor.
2. The `listFiles` method uses a `FilesystemIterator` to iterate over the files in the directory.
3. For each file, it creates an array with the filename, filetype, and filepath.
4. The method returns an array of these file arrays.

**Note**: This class uses the `FilesystemIterator` class, which is part of the SPL (Standard PHP Library). Make sure you have PHP 5.3 or later to use this class.