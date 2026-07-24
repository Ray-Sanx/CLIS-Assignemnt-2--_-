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
