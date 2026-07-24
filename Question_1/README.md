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




Step 1: Set up dummy test files
mkdir -p submissions
echo "Assignment A" > submissions/sub1.txt
echo "Assignment A" > submissions/sub2.txt
echo "Assignment B" > submissions/sub3.txt

Explanation: Created a test directory containing three submission files, where sub1.txt and sub2.txt are identical duplicates. This creates a controlled environment to verify duplicate detection and backup.



Step 2: Make executable and run script

chmod +x find_duplicates_and_backup.sh
./find_duplicates_and_backup.sh

Explanation: Granted execution rights to the shell script using chmod +x and executed it. The script computed file hashes, detected duplicates, backed up unique files, and created separate report and error logs.



Step 3: View generated report file

cat system_report.txt

OUTPUT:

==========================================
       SUBMISSION PROCESSING REPORT       
==========================================
Total Files Processed : 3
Duplicate Files Found : 1
Unique Files Backed Up: 2
Backup Directory      : ./backup



Explanation: Displayed the summary report created via stdout output redirection (>). The report confirms 3 total files evaluated, 1 duplicate flagged, and 2 unique files backed up.

Step 4: Inspect error log

cat error_log.txt

OUTPUT:

Duplicate found: sub2.txt

Explanation: Displayed the contents of error_log.txt populated via standard error redirection (2>>). This keeps operational messages and duplicate warnings separate from the main report output.

