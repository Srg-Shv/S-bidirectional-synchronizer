import os
import shutil
import filecmp
import logging
from datetime import datetime
from concurrent.futures import ThreadPoolExecutor

def setup_logger(log_file='bi-sync.log'):
    """Setup and return a logger."""
    logger = logging.getLogger('bi_sync_logger')
    logger.setLevel(logging.INFO)

    # File handler with UTF-8 encoding
    fh = logging.FileHandler(log_file, encoding='utf-8')
    fh.setLevel(logging.INFO)
    formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')
    fh.setFormatter(formatter)
    logger.addHandler(fh)

    # Console handler for optional console logging
    ch = logging.StreamHandler()
    ch.setLevel(logging.INFO)
    ch.setFormatter(logging.Formatter('%(message)s'))
    logger.addHandler(ch)

    return logger

def copy_file_or_directory(src_path, dest_path, logger):
    """Copy a file or directory from src_path to dest_path."""
    try:
        if os.path.isdir(src_path):
            shutil.copytree(src_path, dest_path, dirs_exist_ok=True)
            logger.info(f"Directory copied: '{dest_path}'")
        else:
            shutil.copy2(src_path, dest_path)
            logger.info(f"File copied: '{dest_path}'")
    except Exception as e:
        logger.error(f"Failed to copy '{src_path}' to '{dest_path}': {e}")

def sync_directories(dir1, dir2, logger, executor):
    """Synchronize two directories bi-directionally."""
    if not os.path.exists(dir1):
        logger.error(f"Directory '{dir1}' does not exist.")
        return
    if not os.path.exists(dir2):
        os.makedirs(dir2)
        logger.info(f"Created directory '{dir2}'.")

    # Compare the directories
    comparison = filecmp.dircmp(dir1, dir2)

    # Parallel processing for copying new files and directories from dir1 to dir2
    futures = []
    for item in comparison.left_only:
        path1 = os.path.join(dir1, item)
        path2 = os.path.join(dir2, item)
        futures.append(executor.submit(copy_file_or_directory, path1, path2, logger))

    # Parallel processing for copying new files and directories from dir2 to dir1
    for item in comparison.right_only:
        path1 = os.path.join(dir1, item)
        path2 = os.path.join(dir2, item)
        futures.append(executor.submit(copy_file_or_directory, path2, path1, logger))

    # Parallel processing for updating files that differ
    for item in comparison.diff_files:
        path1 = os.path.join(dir1, item)
        path2 = os.path.join(dir2, item)
        futures.append(executor.submit(copy_file_or_directory, path1, path2, logger))
        futures.append(executor.submit(copy_file_or_directory, path2, path1, logger))

    # Recursively synchronize common subdirectories
    for item in comparison.common_dirs:
        sync_directories(os.path.join(dir1, item), os.path.join(dir2, item), logger, executor)

    # Wait for all parallel tasks to complete
    for future in futures:
        future.result()

if __name__ == "__main__":
    # Setup logger
    logger = setup_logger()

    # Read directories from 'bi-directories.txt'
    try:
        with open('bi-directories.txt', 'r', encoding='utf-8') as file:
            lines = file.read().splitlines()
            if len(lines) < 2:
                logger.error("The 'bi-directories.txt' file must contain at least two lines: the two directories to synchronize.")
                exit(1)
            directory1 = lines[0]
            directory2 = lines[1]
    except FileNotFoundError:
        logger.error("The 'bi-directories.txt' file was not found.")
        exit(1)
    except Exception as e:
        logger.error(f"An error occurred while reading 'bi-directories.txt': {e}")
        exit(1)

    # Start synchronization
    start_time = datetime.now()
    logger.info("Bi-directional synchronization started.")

    # Use ThreadPoolExecutor for parallel processing
    with ThreadPoolExecutor() as executor:
        sync_directories(directory1, directory2, logger, executor)

    end_time = datetime.now()
    elapsed_time = end_time - start_time
    logger.info(f"Synchronization completed in {elapsed_time}.")

    print("Check 'bi-sync.log' for details.")
