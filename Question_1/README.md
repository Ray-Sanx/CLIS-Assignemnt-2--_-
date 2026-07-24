Question 1: Shell Script for Duplicate Detection & Backup

 1. Shell Script File (`find_duplicates_and_backup.sh`)

```bash
#!/bin/bash

SUBMISSIONS_DIR="./submissions"
BACKUP_DIR="./backup"
REPORT_FILE="system_report.txt"
ERROR_LOG="error_log.txt"

# Clear previous logs
> "$REPORT_FILE" 2> "$ERROR_LOG"
> "$ERROR_LOG"

mkdir -p "$BACKUP_DIR" 2>> "$ERROR_LOG"

if [ ! -d "$SUBMISSIONS_DIR" ]; then
    echo "Error: Submissions directory '$SUBMISSIONS_DIR' not found." >> "$ERROR_LOG"
    exit 1
fi

total_files=0
duplicate_files=0
backed_up_files=0
HASH_FILE=$(mktemp)

for filepath in "$SUBMISSIONS_DIR"/*; do
    if [ ! -f "$filepath" ]; then
        continue
    fi

    total_files=$((total_files + 1))
    file_hash=$(md5sum "$filepath" 2>> "$ERROR_LOG" | awk '{print $1}')
    filename=$(basename "$filepath")

    if grep -q "^$file_hash" "$HASH_FILE" 2>> "$ERROR_LOG"; then
        duplicate_files=$((duplicate_files + 1))
        echo "Duplicate found: $filename" >> "$ERROR_LOG"
    else
        echo "$file_hash $filename" >> "$HASH_FILE"
        cp "$filepath" "$BACKUP_DIR/" 2>> "$ERROR_LOG"
        backed_up_files=$((backed_up_files + 1))
    fi
done

rm -f "$HASH_FILE" 2>> "$ERROR_LOG"

{
    echo "=========================================="
    echo "       SUBMISSION PROCESSING REPORT       "
    echo "=========================================="
    echo "Total Files Processed : $total_files"
    echo "Duplicate Files Found : $duplicate_files"
    echo "Unique Files Backed Up: $backed_up_files"
    echo "Backup Directory      : $BACKUP_DIR"
    echo "=========================================="
} > "$REPORT_FILE"

echo "Processing complete."
