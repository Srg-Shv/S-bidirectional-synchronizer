### Bi-Directional Synchronization Script

This Python script performs **bi-directional synchronization** between two directories, ensuring that both directories are kept in sync. It handles file copying, directory creation, file updates, and even deletions when necessary. The synchronization process works in both directions, meaning changes in either directory will be reflected in the other.

The script also uses **parallel processing** for efficient file operations using Python’s `concurrent.futures.ThreadPoolExecutor`, allowing multiple file and directory operations to be performed simultaneously, speeding up the synchronization process for large directories.

---

### Features:
- **Bi-directional synchronization**: Changes in either directory are mirrored to the other.
- **Parallel processing**: Multiple file operations (copying, updating, removing) are handled concurrently to improve speed.
- **Automatic directory creation**: If one of the directories doesn't exist, it will be automatically created.
- **File update detection**: Files are updated based on modification times.
- **Deletion handling**: Files and directories that exist only in one directory are deleted in the other to maintain synchronization.
- **Logging**: All synchronization activities are logged to a file (`bi-sync.log`) for easy monitoring and debugging.

---

### Prerequisites:
1. **Python 3.8+**: The script requires Python 3.8 or later versions because it utilizes `shutil.copytree` with `dirs_exist_ok=True` and `concurrent.futures`.
2. **Python Libraries**: The script uses the following Python libraries, which are part of the standard library, so no external installations are needed:
   - `os`
   - `shutil`
   - `filecmp`
   - `logging`
   - `concurrent.futures`
   - `datetime`

---

### Usage Instructions:

#### Step 1: Clone the Repository or Download the Script
You can clone the repository or download the script directly from GitHub.

#### Step 2: Create a File for Directory Paths
Create a file named `bi-directories.txt` in the same directory as the script. This file should contain two lines:
- The **first line** is the path to the first directory.
- The **second line** is the path to the second directory.

Example `bi-directories.txt`:
```
/path/to/directory1
/path/to/directory2
```

#### Step 3: Run the Script
To run the bi-directional synchronization script, execute the following command:

```bash
python bi_sync.py
```

The script will read the directories specified in `bi-directories.txt` and start the synchronization process. It will log all activities, including copying, updating, and deleting files, to a log file named `bi-sync.log`.

#### Step 4: Monitor the Log File
You can check the `bi-sync.log` file for detailed logs of the synchronization process:

```bash
cat bi-sync.log
```

#### Step 5: Check the Synchronized Directories
After the script completes, both directories specified in `bi-directories.txt` will be synchronized with each other, with the changes reflected in both locations.

---

### Example Use Case

Suppose you have two directories, `/home/user/dir1` and `/home/user/dir2`, and you want to keep their contents synchronized. You would:

1. Create the `bi-directories.txt` file with the following contents:

   ```
   /home/user/dir1
   /home/user/dir2
   ```

2. Run the script:

   ```bash
   python bi_sync.py
   ```

3. The script will ensure that files from `dir1` are mirrored to `dir2` and vice versa, handling new files, modifications, and deletions in either directory.

---

### Error Handling

- **Missing Directories**: If one of the directories does not exist, it will automatically be created.
- **File Permission Issues**: Any permission-related errors during file operations will be logged, but the script will continue executing.
- **Invalid `bi-directories.txt` File**: If the `bi-directories.txt` file is missing or does not contain two valid paths, an error will be logged, and the script will exit.

---

### Logging Details

All synchronization operations are logged in detail. Each log entry contains:
- **Timestamp**: The exact time of the operation.
- **Log Level**: Information, errors, and warnings.
- **Message**: A description of the action performed, such as file copied, directory created, or file removed.

Example log output:
```
2024-10-03 14:32:10 - INFO - File copied: '/home/user/dir1/file.txt'
2024-10-03 14:32:12 - INFO - Directory removed: '/home/user/dir2/old_folder'
2024-10-03 14:32:15 - INFO - File updated: '/home/user/dir2/important.txt'
```

---

### Notes:
- The script assumes both directories should be identical after synchronization, so any files or directories that exist only in one directory will be copied to the other or deleted as needed.
- Ensure you have sufficient disk space and permissions for both directories before running the script.

---

This script provides an efficient and easy way to ensure two directories are always in sync, with robust error handling and logging to track changes.
