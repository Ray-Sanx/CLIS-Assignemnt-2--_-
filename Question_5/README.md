Question 5: Evaluation & Justification of vi/vim Crash Recovery Strategies

 1. Evaluation of vi/vim Recovery Mechanisms

| Recovery Mechanism | Description | Behavior During System Crash | Reliability Assessment |
| :--- | :--- | :--- | :--- |
| **Swap Files (`.swp`)** | Hidden binary files (`.filename.swp`) created in real-time by `vi` to log unwritten buffer changes to disk. | **Persists across system crashes.** Automatically detected when reopening the file in `vi`. | **HIGHEST** |
| **Undo History** | In-memory tree tracking keystrokes and line modifications during an active editing session. | **Lost instantly** upon crash because undo nodes reside solely in volatile RAM. | **POOR** |
| **Registers** | Clipboard memory buffers (`a-z`, `0-9`) storing copied or cut text snippets. | **Lost** during crash as registers exist purely in process memory. | **POOR** |
| **Backup Files (`filename~`)** | Duplicate copies of the original file created prior to saving write calls (`:w`). | Only protects the state of the file **prior to the edit session**. Unsaved edits made right before the crash are lost. | **MODERATE** |
| **Auto-Recovery (`vi -r`)** | Command-line recovery mode designed to scan and recover buffer states from `.swp` files automatically. | Uses the `.swp` file on disk to reconstruct unsaved work. | **HIGH (Execution Utility)** |



 2. Step-by-Step Crash Recovery Execution Log & Explanation

 Step 1: Identify the hidden swap file

ls -la .app_config.conf.swp

Explanation: Located the hidden .app_config.conf.swp file preserved on disk by vi's buffer management prior to the system crash.



Step 2: Recover the editing session using vi -r
Bash

vi -r app_config.conf

Explanation: Executed vi -r to load the unsaved buffer state directly from the swap file into the vi editing workspace.



Step 3: Save the recovered buffer

Inside vi command mode:
Plaintext

:w app_config_recovered.conf
:q!

Explanation: Saved the reconstructed buffer state to a stable file path (app_config_recovered.conf) and exited vi cleanly.



Step 4: Remove the orphan swap file
Bash

rm .app_config.conf.swp

Explanation: Removed the remaining swap file to prevent redundant crash warnings from appearing when editing app_config.conf in the future.

3. Proposal & Justification of the Most Reliable Strategy 
Proposed Strategy: Swap File Recovery via Auto-Recovery (vi -r)
Justification:

    Persistence Across Failures: Swap files continuously write buffer changes directly to non-volatile disk storage during an editing session. They survive sudden power outages, OS panics, and unexpected terminal disconnects.

    Data Completeness: Unlike backup files (which only hold the pre-session state of the file before editing began), swap files preserve unsaved edits right up to the moment of the crash.

    Integrity Validation: vi -r validates swap metadata, process IDs, and timestamps before restoring, ensuring complete data recovery without file corruption.
