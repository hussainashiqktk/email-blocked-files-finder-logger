Python script that continuously monitors a directory and its subdirectories for the specified file types. When a new file of these types is created, the script logs detailed information about the file, including its path, creation time, and file size, to a log file in another location.

The script uses the `watchdog` library for efficient filesystem monitoring. If you don’t have it installed, you can install it with:

```bash
pip install watchdog
```

### Script

```python
import os
import time
import logging
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler

# Configure logging
log_directory = "/path/to/log/directory"
if not os.path.exists(log_directory):
    os.makedirs(log_directory)
logging.basicConfig(filename=os.path.join(log_directory, 'file_creation_logs.log'),
                    level=logging.INFO,
                    format='%(asctime)s - %(message)s')

# File extensions to monitor
file_extensions = {'.js', '.jse', '.vb', '.vbe', '.vbs', '.ws', '.wsh', '.wsf', '.scr', '.pif', '.hta', '.ISO'}

class FileCreationHandler(FileSystemEventHandler):
    def on_created(self, event):
        # Only act on files with specified extensions
        if event.is_directory:
            return
        file_extension = os.path.splitext(event.src_path)[1]
        if file_extension in file_extensions:
            file_info = {
                'file_path': event.src_path,
                'file_size': os.path.getsize(event.src_path),
                'created_at': time.ctime(os.path.getctime(event.src_path))
            }
            # Log file details
            logging.info("New file detected: %s", file_info)

def monitor_directory(directory_to_watch):
    event_handler = FileCreationHandler()
    observer = Observer()
    observer.schedule(event_handler, directory_to_watch, recursive=True)
    observer.start()
    print(f"Monitoring directory {directory_to_watch} for specified file types...")

    try:
        while True:
            time.sleep(1)  # Keep script running
    except KeyboardInterrupt:
        observer.stop()
        print("Monitoring stopped.")
    observer.join()

# Set the directory you want to monitor
directory_to_watch = "/path/to/monitor/directory"
monitor_directory(directory_to_watch)
```

### Explanation

1. **Directory Monitoring**: The script monitors a specified directory (and its subdirectories) for the listed file types.
2. **File Logging**: When a new file with a specified extension is created, it logs:
   - The full file path
   - The file size
   - The creation timestamp
3. **Logging Output**: Logs are written to `file_creation_logs.log` in the specified `log_directory`.

### Usage

1. Replace `/path/to/monitor/directory` with the directory you want to monitor.
2. Replace `/path/to/log/directory` with the directory where you want to store the log file.

This script will run continuously, and you can stop it anytime with `Ctrl + C`. Let me know if you have any questions!
