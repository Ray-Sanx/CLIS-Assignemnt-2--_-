Question 4: Real-Time Log Monitoring Pipeline
 1. Shell Script (`monitor_logs.sh`)


#!/bin/bash

LOG_FILE="server.log"
REPORT_FILE="error_report.txt"

touch "$LOG_FILE"
echo "[INFO] Monitoring $LOG_FILE for ERROR entries..."

# Real-time command pipeline
tail -f -n 0 "$LOG_FILE" | grep --line-buffered "ERROR" >> "$REPORT_FILE" 2> /dev/null



2. Execution Log & Step-by-Step Explanation
Step 1: Launch background monitor
Bash

touch server.log
chmod +x monitor_logs.sh
./monitor_logs.sh &
MONITOR_PID=$!

Explanation: Initialized the server.log file, granted execution rights to the monitoring script using chmod +x, and ran the process in the background (&), capturing its Process ID (MONITOR_PID).


Step 2: Simulate real-time log entries


echo "[2026-07-24 10:00:01] INFO User admin logged in" >> server.log
echo "[2026-07-24 10:00:02] ERROR Database connection failed" >> server.log
echo "[2026-07-24 10:00:03] DEBUG Cache cleared" >> server.log
echo "[2026-07-24 10:00:04] ERROR Out of memory exception" >> server.log

 Explanation: Appended simulated live server events to server.log. The running background pipeline automatically intercepted and processed the new lines as they were added.



 Step 3: Verify error report and terminate process

cat error_report.txt
kill $MONITOR_PID

Output:


[2026-07-24 10:00:02] ERROR Database connection failed
[2026-07-24 10:00:04] ERROR Out of memory exception

 Explanation: Inspected error_report.txt to confirm that standard INFO and DEBUG logs were ignored while ERROR events were captured instantly in real time, then stopped the background process using kill.



3. Command Pipeline & Efficiency Explanation

    Pipes (|): Pass output directly from tail to grep in memory via kernel FIFO buffers without reading/writing intermediate files to disk, eliminating redundant I/O operations.

    tail -f -n 0: Monitors the log file continuously for new appended lines. Using -n 0 ignores existing old logs and focuses strictly on live, incoming entries.

    grep --line-buffered "ERROR": Filters input for the keyword "ERROR". The --line-buffered flag forces grep to write matches out line-by-line instantly instead of filling up memory buffers, delivering immediate real-time output.

    Append Redirection (>>): Continuously appends matched error lines to error_report.txt without overwriting historical log data.

    2> /dev/null: Redirects standard error (stderr) to the system null device to suppress unwanted warnings or status messages from cluttering the terminal.
