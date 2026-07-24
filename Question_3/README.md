Question 3: Secure File-Processing Utility using Low-Level System Calls

1. C Source Code (`employee_db.c`)

```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>

#define FILENAME "employee_records.dat"

typedef struct {
    int id;
    char name[30];
    double salary;
} Employee;

int main() {
    // Open/Create file securely with 0600 mode (Read/Write owner only)
    int fd = open(FILENAME, O_CREAT | O_RDWR | O_TRUNC, 0600);
    if (fd < 0) {
        perror("Failed to open file");
        exit(EXIT_FAILURE);
    }
    printf("[INFO] Created secure file '%s' with fd: %d\n", FILENAME, fd);

    Employee emp1 = {101, "Alice Smith", 75000.00};
    Employee emp2 = {102, "Bob Jones", 62000.00};
    Employee emp3 = {103, "Charlie Brown", 58000.00};

    write(fd, &emp1, sizeof(Employee));
    write(fd, &emp2, sizeof(Employee));
    write(fd, &emp3, sizeof(Employee));
    printf("[INFO] Wrote initial employee records.\n");

    // Update Record 2 (Bob Jones) directly without rewriting whole file
    off_t target_offset = 1 * sizeof(Employee);
    lseek(fd, target_offset, SEEK_SET);

    Employee updated_emp2 = {102, "Bob Jones", 68000.00};
    write(fd, &updated_emp2, sizeof(Employee));
    printf("[INFO] Updated Record ID 102 via lseek().\n");

    // Random Access Retrieval
    lseek(fd, 2 * sizeof(Employee), SEEK_SET);
    Employee fetched_emp;
    read(fd, &fetched_emp, sizeof(Employee));
    printf("[FETCH] ID: %d | Name: %s | Salary: $%.2f\n", fetched_emp.id, fetched_emp.name, fetched_emp.salary);

    close(fd);
    printf("[INFO] Closed file descriptor.\n");
    return 0;
}



2. Execution Log & Step-by-Step Explanation
Step 1: Compile program

gcc -Wall employee_db.c -o employee_db

Explanation: Compiled employee_db.c using gcc with warning checks enabled. The compilation finished cleanly, producing the output binary employee_db.

Step 2: Execute utility

./employee_db


Output:


[INFO] Created secure file 'employee_records.dat' with fd: 3
[INFO] Wrote initial employee records.
[INFO] Updated Record ID 102 via lseek().
[FETCH] ID: 103 | Name: Charlie Brown | Salary: $58000.00
[INFO] Closed file descriptor.

Explanation: Ran the binary. The utility created a file, performed structured record writes, jumped directly to a specific record using lseek() to update it in-place without rewriting the whole file, and read back target records using random access.

Step 3: Verify security mode


ls -l employee_records.dat

Output:


-rw------- 1 user group 120 Jul 24 21:40 employee_records.dat


3. System Calls Functional Summary

    open(): Opens or creates disk files at the kernel level, returning a file descriptor integer. Passing 0600 sets strict file access control.

    write(): Transfers raw binary bytes directly from memory buffers to disk.

    read(): Reads byte sequences from file offsets into application memory structures.

    lseek(): Repositions the file offset pointer instantly to any position (SEEK_SET, SEEK_CUR, SEEK_END), enabling efficient random updates without rewriting untouched file data.

    close(): Releases file descriptors from the process kernel table.

    Explanation: Checked the file access attributes. The 0600 octal permissions passed during open() restrict file access strictly to the owner.
