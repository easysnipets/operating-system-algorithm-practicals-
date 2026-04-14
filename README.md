# Lab-IV-Operating-System-Algorithm-Practicals

---

## Practical 1

**Aim:** Write a program to demonstrate the use of FCFS CPU Scheduling algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
FCFS (First Come First Serve) is the simplest CPU scheduling algorithm. Processes are executed in the order they arrive in the ready queue. It is non-preemptive, meaning once a process starts executing, it runs to completion.

- **Turnaround Time (TAT)** = Completion Time − Arrival Time
- **Waiting Time (WT)** = Turnaround Time − Burst Time
- **Average TAT** = Sum of all TAT / Number of processes
- **Average WT** = Sum of all WT / Number of processes

The main drawback is the **Convoy Effect**, where shorter processes wait behind longer ones.

**Steps:**
Open VS Code → Create `fcfs.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int n, i;
    printf("Enter number of processes: ");
    scanf("%d", &n);

    int pid[n], burst[n], arrival[n];
    int completion[n], waiting[n], turnaround[n];

    for (i = 0; i < n; i++) {
        pid[i] = i + 1;
        printf("Enter arrival time and burst time for P%d: ", pid[i]);
        scanf("%d %d", &arrival[i], &burst[i]);
    }

    // Calculate completion time
    completion[0] = arrival[0] + burst[0];
    for (i = 1; i < n; i++) {
        if (completion[i-1] < arrival[i])
            completion[i] = arrival[i] + burst[i];
        else
            completion[i] = completion[i-1] + burst[i];
    }

    float avgWT = 0, avgTAT = 0;

    printf("\nPID\tArrival\tBurst\tCompletion\tTAT\tWT\n");
    for (i = 0; i < n; i++) {
        turnaround[i] = completion[i] - arrival[i];
        waiting[i] = turnaround[i] - burst[i];
        avgTAT += turnaround[i];
        avgWT += waiting[i];
        printf("P%d\t%d\t%d\t%d\t\t%d\t%d\n",
               pid[i], arrival[i], burst[i], completion[i], turnaround[i], waiting[i]);
    }

    printf("\nAverage Turnaround Time: %.2f", avgTAT / n);
    printf("\nAverage Waiting Time: %.2f\n", avgWT / n);

    return 0;
}
```

**Output:**
```
Enter number of processes: 3
Enter arrival time and burst time for P1: 0 5
Enter arrival time and burst time for P2: 1 3
Enter arrival time and burst time for P3: 2 8

PID     Arrival Burst   Completion      TAT     WT
P1      0       5       5               5       0
P2      1       3       8               7       4
P3      2       8       16              14      6

Average Turnaround Time: 8.67
Average Waiting Time: 3.33
```

---

## Practical 2

**Aim:** Write a program to demonstrate the use of SJF CPU Scheduling algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
SJF (Shortest Job First) schedules the process with the smallest burst time next. It can be preemptive (SRTF) or non-preemptive. This algorithm gives the minimum average waiting time for a given set of processes.

- **Turnaround Time (TAT)** = Completion Time − Arrival Time
- **Waiting Time (WT)** = TAT − Burst Time

The main challenge with SJF is predicting the burst time of a process in advance.

**Steps:**
Open VS Code → Create `sjf.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int n, i, j, temp;
    printf("Enter number of processes: ");
    scanf("%d", &n);

    int pid[n], burst[n], arrival[n];
    int completion[n], waiting[n], turnaround[n];
    int visited[n];

    for (i = 0; i < n; i++) {
        pid[i] = i + 1;
        visited[i] = 0;
        printf("Enter arrival time and burst time for P%d: ", pid[i]);
        scanf("%d %d", &arrival[i], &burst[i]);
    }

    // Sort by arrival time
    for (i = 0; i < n - 1; i++) {
        for (j = 0; j < n - i - 1; j++) {
            if (arrival[j] > arrival[j+1]) {
                temp = arrival[j]; arrival[j] = arrival[j+1]; arrival[j+1] = temp;
                temp = burst[j];   burst[j]   = burst[j+1];   burst[j+1]   = temp;
                temp = pid[j];     pid[j]     = pid[j+1];     pid[j+1]     = temp;
            }
        }
    }

    int time = 0, done = 0;
    while (done < n) {
        int minBurst = 9999, idx = -1;
        for (i = 0; i < n; i++) {
            if (!visited[i] && arrival[i] <= time && burst[i] < minBurst) {
                minBurst = burst[i];
                idx = i;
            }
        }
        if (idx == -1) { time++; continue; }
        time += burst[idx];
        completion[idx] = time;
        turnaround[idx] = completion[idx] - arrival[idx];
        waiting[idx] = turnaround[idx] - burst[idx];
        visited[idx] = 1;
        done++;
    }

    float avgWT = 0, avgTAT = 0;
    printf("\nPID\tArrival\tBurst\tCompletion\tTAT\tWT\n");
    for (i = 0; i < n; i++) {
        avgTAT += turnaround[i];
        avgWT += waiting[i];
        printf("P%d\t%d\t%d\t%d\t\t%d\t%d\n",
               pid[i], arrival[i], burst[i], completion[i], turnaround[i], waiting[i]);
    }

    printf("\nAverage Turnaround Time: %.2f", avgTAT / n);
    printf("\nAverage Waiting Time: %.2f\n", avgWT / n);

    return 0;
}
```

**Output:**
```
Enter number of processes: 3
Enter arrival time and burst time for P1: 0 6
Enter arrival time and burst time for P2: 1 3
Enter arrival time and burst time for P3: 2 1

PID     Arrival Burst   Completion      TAT     WT
P1      0       6       6               6       0
P3      2       1       7               5       4
P2      1       3       10              9       6

Average Turnaround Time: 6.67
Average Waiting Time: 3.33
```

---

## Practical 3

**Aim:** Write a program to demonstrate the use of Priority CPU Scheduling algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
In Priority Scheduling, each process is assigned a priority number. The CPU is allocated to the process with the highest priority (usually the lowest number = highest priority). It can be preemptive or non-preemptive.

- **Turnaround Time (TAT)** = Completion Time − Arrival Time
- **Waiting Time (WT)** = TAT − Burst Time

A major problem is **Starvation**, where low-priority processes may wait indefinitely. This is solved by **Aging** (gradually increasing priority over time).

**Steps:**
Open VS Code → Create `priority.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int n, i, j, temp;
    printf("Enter number of processes: ");
    scanf("%d", &n);

    int pid[n], burst[n], arrival[n], priority[n];
    int completion[n], waiting[n], turnaround[n];
    int visited[n];

    for (i = 0; i < n; i++) {
        pid[i] = i + 1;
        visited[i] = 0;
        printf("Enter arrival time, burst time, and priority for P%d: ", pid[i]);
        scanf("%d %d %d", &arrival[i], &burst[i], &priority[i]);
    }

    int time = 0, done = 0;
    while (done < n) {
        int highPri = 9999, idx = -1;
        for (i = 0; i < n; i++) {
            if (!visited[i] && arrival[i] <= time && priority[i] < highPri) {
                highPri = priority[i];
                idx = i;
            }
        }
        if (idx == -1) { time++; continue; }
        time += burst[idx];
        completion[idx] = time;
        turnaround[idx] = completion[idx] - arrival[idx];
        waiting[idx] = turnaround[idx] - burst[idx];
        visited[idx] = 1;
        done++;
    }

    float avgWT = 0, avgTAT = 0;
    printf("\nPID\tArrival\tBurst\tPriority\tCompletion\tTAT\tWT\n");
    for (i = 0; i < n; i++) {
        avgTAT += turnaround[i];
        avgWT += waiting[i];
        printf("P%d\t%d\t%d\t%d\t\t%d\t\t%d\t%d\n",
               pid[i], arrival[i], burst[i], priority[i], completion[i], turnaround[i], waiting[i]);
    }

    printf("\nAverage Turnaround Time: %.2f", avgTAT / n);
    printf("\nAverage Waiting Time: %.2f\n", avgWT / n);

    return 0;
}
```

**Output:**
```
Enter number of processes: 3
Enter arrival time, burst time, and priority for P1: 0 4 2
Enter arrival time, burst time, and priority for P2: 1 3 1
Enter arrival time, burst time, and priority for P3: 2 5 3

PID     Arrival Burst   Priority        Completion      TAT     WT
P1      0       4       2               4               4       0
P2      1       3       1               7               6       3
P3      2       5       3               12              10      5

Average Turnaround Time: 6.67
Average Waiting Time: 2.67
```

---

## Practical 4

**Aim:** Write a program to demonstrate the use of Round Robin CPU Scheduling algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
Round Robin is a preemptive scheduling algorithm designed especially for time-sharing systems. Each process is given a fixed time slot called a **Time Quantum (TQ)**. If a process doesn't finish within its quantum, it is placed back at the end of the ready queue.

- **Turnaround Time (TAT)** = Completion Time − Arrival Time
- **Waiting Time (WT)** = TAT − Burst Time

Smaller time quantum leads to better response time but increases context switching overhead.

**Steps:**
Open VS Code → Create `roundrobin.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int n, tq, i, time = 0;
    printf("Enter number of processes: ");
    scanf("%d", &n);
    printf("Enter time quantum: ");
    scanf("%d", &tq);

    int pid[n], burst[n], remaining[n], arrival[n];
    int completion[n], waiting[n], turnaround[n];

    for (i = 0; i < n; i++) {
        pid[i] = i + 1;
        printf("Enter arrival and burst time for P%d: ", pid[i]);
        scanf("%d %d", &arrival[i], &burst[i]);
        remaining[i] = burst[i];
    }

    int done = 0;
    while (done < n) {
        int flag = 0;
        for (i = 0; i < n; i++) {
            if (remaining[i] > 0 && arrival[i] <= time) {
                flag = 1;
                if (remaining[i] <= tq) {
                    time += remaining[i];
                    remaining[i] = 0;
                    completion[i] = time;
                    turnaround[i] = completion[i] - arrival[i];
                    waiting[i] = turnaround[i] - burst[i];
                    done++;
                } else {
                    time += tq;
                    remaining[i] -= tq;
                }
            }
        }
        if (!flag) time++;
    }

    float avgWT = 0, avgTAT = 0;
    printf("\nPID\tArrival\tBurst\tCompletion\tTAT\tWT\n");
    for (i = 0; i < n; i++) {
        avgTAT += turnaround[i];
        avgWT += waiting[i];
        printf("P%d\t%d\t%d\t%d\t\t%d\t%d\n",
               pid[i], arrival[i], burst[i], completion[i], turnaround[i], waiting[i]);
    }

    printf("\nAverage Turnaround Time: %.2f", avgTAT / n);
    printf("\nAverage Waiting Time: %.2f\n", avgWT / n);

    return 0;
}
```

**Output:**
```
Enter number of processes: 3
Enter time quantum: 2
Enter arrival and burst time for P1: 0 5
Enter arrival and burst time for P2: 1 4
Enter arrival and burst time for P3: 2 3

PID     Arrival Burst   Completion      TAT     WT
P1      0       5       11              11      6
P2      1       4       10              9       5
P3      2       3       9               7       4

Average Turnaround Time: 9.00
Average Waiting Time: 5.00
```

---

## Practical 5

**Aim:** Write a program to demonstrate the use of FIFO Page Replacement algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
FIFO (First In First Out) is the simplest page replacement algorithm. When a page fault occurs and all frames are full, the page that has been in memory the longest is replaced.

- **Page Fault** = When a required page is not found in any frame
- **Hit Ratio** = (Total References − Page Faults) / Total References

FIFO suffers from **Belady's Anomaly**: increasing the number of frames can sometimes increase the number of page faults.

**Steps:**
Open VS Code → Create `fifo.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int frames, pages, i, j, k;
    printf("Enter number of frames: ");
    scanf("%d", &frames);
    printf("Enter number of pages in reference string: ");
    scanf("%d", &pages);

    int ref[pages], frame[frames];
    printf("Enter reference string: ");
    for (i = 0; i < pages; i++)
        scanf("%d", &ref[i]);

    for (i = 0; i < frames; i++)
        frame[i] = -1;

    int pageFaults = 0, pointer = 0;

    printf("\nPage\tFrames\n");
    for (i = 0; i < pages; i++) {
        int found = 0;
        for (j = 0; j < frames; j++) {
            if (frame[j] == ref[i]) { found = 1; break; }
        }
        if (!found) {
            frame[pointer] = ref[i];
            pointer = (pointer + 1) % frames;
            pageFaults++;
            printf("%d\t", ref[i]);
            for (k = 0; k < frames; k++)
                frame[k] == -1 ? printf("- ") : printf("%d ", frame[k]);
            printf("<-- Page Fault\n");
        } else {
            printf("%d\t", ref[i]);
            for (k = 0; k < frames; k++)
                printf("%d ", frame[k]);
            printf("\n");
        }
    }

    printf("\nTotal Page Faults: %d\n", pageFaults);
    printf("Hit Ratio: %.2f\n", (float)(pages - pageFaults) / pages);

    return 0;
}
```

**Output:**
```
Enter number of frames: 3
Enter number of pages in reference string: 8
Enter reference string: 1 2 3 4 1 2 5 1

Page    Frames
1       1 - -  <-- Page Fault
2       1 2 -  <-- Page Fault
3       1 2 3  <-- Page Fault
4       4 2 3  <-- Page Fault
1       4 1 3  <-- Page Fault
2       4 1 2  <-- Page Fault
5       5 1 2  <-- Page Fault
1       5 1 2

Total Page Faults: 7
Hit Ratio: 0.12
```

---

## Practical 6

**Aim:** Write a program to demonstrate the use of LRU Page Replacement algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
LRU (Least Recently Used) replaces the page that has not been used for the longest period of time. It is based on the principle of **locality of reference**, which states that recently used pages are likely to be used again soon.

- **Page Fault** = When a required page is not found in any frame
- **Hit Ratio** = (Total References − Page Faults) / Total References

LRU performs better than FIFO in most cases but requires tracking of page usage history.

**Steps:**
Open VS Code → Create `lru.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int frames, pages, i, j, k;
    printf("Enter number of frames: ");
    scanf("%d", &frames);
    printf("Enter number of pages in reference string: ");
    scanf("%d", &pages);

    int ref[pages], frame[frames], lastUsed[frames];
    printf("Enter reference string: ");
    for (i = 0; i < pages; i++)
        scanf("%d", &ref[i]);

    for (i = 0; i < frames; i++) {
        frame[i] = -1;
        lastUsed[i] = 0;
    }

    int pageFaults = 0, count = 0;

    printf("\nPage\tFrames\n");
    for (i = 0; i < pages; i++) {
        int found = 0;
        count++;

        for (j = 0; j < frames; j++) {
            if (frame[j] == ref[i]) {
                found = 1;
                lastUsed[j] = count;
                break;
            }
        }

        if (!found) {
            int lruIdx = 0;
            for (j = 1; j < frames; j++) {
                if (lastUsed[j] < lastUsed[lruIdx])
                    lruIdx = j;
            }
            frame[lruIdx] = ref[i];
            lastUsed[lruIdx] = count;
            pageFaults++;

            printf("%d\t", ref[i]);
            for (k = 0; k < frames; k++)
                frame[k] == -1 ? printf("- ") : printf("%d ", frame[k]);
            printf("<-- Page Fault\n");
        } else {
            printf("%d\t", ref[i]);
            for (k = 0; k < frames; k++)
                printf("%d ", frame[k]);
            printf("\n");
        }
    }

    printf("\nTotal Page Faults: %d\n", pageFaults);
    printf("Hit Ratio: %.2f\n", (float)(pages - pageFaults) / pages);

    return 0;
}
```

**Output:**
```
Enter number of frames: 3
Enter number of pages in reference string: 8
Enter reference string: 1 2 3 4 1 2 5 1

Page    Frames
1       1 - -  <-- Page Fault
2       1 2 -  <-- Page Fault
3       1 2 3  <-- Page Fault
4       4 2 3  <-- Page Fault
1       4 1 3  <-- Page Fault
2       4 1 2  <-- Page Fault
5       5 1 2  <-- Page Fault
1       5 1 2

Total Page Faults: 7
Hit Ratio: 0.12
```

---

## Practical 7

**Aim:** Write a program to demonstrate the use of the Deadlock Detection algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
A **Deadlock** is a situation where a set of processes are blocked because each process is holding a resource and waiting to acquire a resource held by another. The detection algorithm uses the **Resource Allocation Graph** or **Banker's-like** approach.

The algorithm checks:
- **Allocation Matrix** – Resources currently held by each process
- **Request Matrix** – Resources still needed by each process
- **Available Vector** – Resources currently available

If a safe sequence exists, no deadlock is detected. Otherwise, deadlock exists.

**Steps:**
Open VS Code → Create `deadlock.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int n, r, i, j, k;
    printf("Enter number of processes and resource types: ");
    scanf("%d %d", &n, &r);

    int alloc[n][r], request[n][r], avail[r];
    int finish[n], safeSeq[n];

    printf("Enter Allocation Matrix:\n");
    for (i = 0; i < n; i++)
        for (j = 0; j < r; j++)
            scanf("%d", &alloc[i][j]);

    printf("Enter Request Matrix:\n");
    for (i = 0; i < n; i++)
        for (j = 0; j < r; j++)
            scanf("%d", &request[i][j]);

    printf("Enter Available Resources: ");
    for (j = 0; j < r; j++)
        scanf("%d", &avail[j]);

    for (i = 0; i < n; i++) finish[i] = 0;

    int count = 0;
    while (count < n) {
        int found = 0;
        for (i = 0; i < n; i++) {
            if (!finish[i]) {
                int canRun = 1;
                for (j = 0; j < r; j++) {
                    if (request[i][j] > avail[j]) { canRun = 0; break; }
                }
                if (canRun) {
                    for (j = 0; j < r; j++)
                        avail[j] += alloc[i][j];
                    safeSeq[count++] = i;
                    finish[i] = 1;
                    found = 1;
                }
            }
        }
        if (!found) break;
    }

    if (count == n) {
        printf("\nNo Deadlock Detected.\nSafe Sequence: ");
        for (i = 0; i < n; i++)
            printf("P%d ", safeSeq[i]);
        printf("\n");
    } else {
        printf("\nDeadlock Detected!\nDeadlocked Processes: ");
        for (i = 0; i < n; i++)
            if (!finish[i]) printf("P%d ", i);
        printf("\n");
    }

    return 0;
}
```

**Output:**
```
Enter number of processes and resource types: 4 3
Enter Allocation Matrix:
0 1 0
2 0 0
3 0 3
2 1 1
Enter Request Matrix:
0 0 0
2 0 2
0 0 0
1 0 0
Enter Available Resources: 0 0 0

Deadlock Detected!
Deadlocked Processes: P1 P3
```

---

## Practical 8

**Aim:** Write a program to demonstrate the use of FCFS Disk Scheduling algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
FCFS (First Come First Serve) Disk Scheduling services disk requests in the order they arrive. The disk head moves from one track to the next as requests come in.

- **Seek Time** = Number of tracks the disk head moves to reach the requested track
- **Total Seek Time** = Sum of all individual seek movements
- **Average Seek Time** = Total Seek Time / Number of Requests

FCFS is fair and simple but does not optimize disk head movement.

**Steps:**
Open VS Code → Create `fcfs_disk.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int n, head, i;
    printf("Enter initial head position: ");
    scanf("%d", &head);
    printf("Enter number of disk requests: ");
    scanf("%d", &n);

    int req[n];
    printf("Enter disk request queue: ");
    for (i = 0; i < n; i++)
        scanf("%d", &req[i]);

    int totalSeek = 0, curr = head;

    printf("\nHead Movement:\n%d", curr);
    for (i = 0; i < n; i++) {
        int seek = abs(req[i] - curr);
        totalSeek += seek;
        curr = req[i];
        printf(" -> %d", curr);
    }

    printf("\n\nTotal Seek Time: %d", totalSeek);
    printf("\nAverage Seek Time: %.2f\n", (float)totalSeek / n);

    return 0;
}
```

**Output:**
```
Enter initial head position: 50
Enter number of disk requests: 6
Enter disk request queue: 82 170 43 140 24 16

Head Movement:
50 -> 82 -> 170 -> 43 -> 140 -> 24 -> 16

Total Seek Time: 642
Average Seek Time: 107.00
```

---

## Practical 9

**Aim:** Write a program to demonstrate the use of SCAN Disk Scheduling algorithm.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
SCAN (also called the Elevator Algorithm) moves the disk head in one direction servicing all requests along the way, and then reverses direction when it reaches the end. This prevents starvation and provides more uniform wait times.

- **Total Seek Time** = Sum of all head movements
- **Average Seek Time** = Total Seek Time / Number of Requests

SCAN is more efficient than FCFS and SSTF in heavy load scenarios.

**Steps:**
Open VS Code → Create `scan.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int n, head, i, j, temp, diskSize;
    printf("Enter disk size: ");
    scanf("%d", &diskSize);
    printf("Enter initial head position: ");
    scanf("%d", &head);
    printf("Enter number of disk requests: ");
    scanf("%d", &n);

    int req[n];
    printf("Enter disk request queue: ");
    for (i = 0; i < n; i++)
        scanf("%d", &req[i]);

    // Sort requests
    for (i = 0; i < n - 1; i++)
        for (j = 0; j < n - i - 1; j++)
            if (req[j] > req[j+1]) {
                temp = req[j]; req[j] = req[j+1]; req[j+1] = temp;
            }

    // Find position of head in sorted array
    int pos = 0;
    while (pos < n && req[pos] < head) pos++;

    int totalSeek = 0, curr = head;
    printf("\nHead Movement:\n%d", curr);

    // Move toward higher tracks first
    for (i = pos; i < n; i++) {
        totalSeek += abs(req[i] - curr);
        curr = req[i];
        printf(" -> %d", curr);
    }

    // Move toward lower tracks
    for (i = pos - 1; i >= 0; i--) {
        totalSeek += abs(req[i] - curr);
        curr = req[i];
        printf(" -> %d", curr);
    }

    printf("\n\nTotal Seek Time: %d", totalSeek);
    printf("\nAverage Seek Time: %.2f\n", (float)totalSeek / n);

    return 0;
}
```

**Output:**
```
Enter disk size: 200
Enter initial head position: 50
Enter number of disk requests: 6
Enter disk request queue: 82 170 43 140 24 16

Head Movement:
50 -> 82 -> 140 -> 170 -> 43 -> 24 -> 16

Total Seek Time: 322
Average Seek Time: 53.67
```

---

## Practical 10

**Aim:** Write a program to simulate the paging technique of memory management.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
**Paging** divides physical memory into fixed-size blocks called **frames** and logical memory into blocks of the same size called **pages**. A **Page Table** maps logical page numbers to physical frame numbers.

Key formulas:
- **Logical Address** = Page Number × Page Size + Offset
- **Physical Address** = Frame Number × Page Size + Offset
- **Number of Pages** = Process Size / Page Size
- **Internal Fragmentation** = Page Size − (Process Size mod Page Size)

Paging eliminates external fragmentation but may cause internal fragmentation.

**Steps:**
Open VS Code → Create `paging.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int pageSize, processSize, frames, i;

    printf("Enter page size (in bytes): ");
    scanf("%d", &pageSize);
    printf("Enter process size (in bytes): ");
    scanf("%d", &processSize);

    int numPages = (processSize + pageSize - 1) / pageSize;
    printf("Enter number of frames in physical memory: ");
    scanf("%d", &frames);

    int pageTable[numPages];
    printf("Enter frame numbers for each page:\n");
    for (i = 0; i < numPages; i++) {
        printf("  Page %d -> Frame: ", i);
        scanf("%d", &pageTable[i]);
    }

    printf("\n--- Page Table ---\n");
    printf("Page No.\tFrame No.\n");
    for (i = 0; i < numPages; i++)
        printf("%d\t\t%d\n", i, pageTable[i]);

    int logicalAddr;
    printf("\nEnter logical address to translate: ");
    scanf("%d", &logicalAddr);

    int pageNum = logicalAddr / pageSize;
    int offset   = logicalAddr % pageSize;

    if (pageNum >= numPages) {
        printf("Error: Page number out of range!\n");
    } else {
        int physicalAddr = pageTable[pageNum] * pageSize + offset;
        printf("\nLogical Address  : %d", logicalAddr);
        printf("\nPage Number      : %d", pageNum);
        printf("\nOffset           : %d", offset);
        printf("\nPhysical Address : %d\n", physicalAddr);
    }

    int internalFrag = numPages * pageSize - processSize;
    printf("\nInternal Fragmentation: %d bytes\n", internalFrag);

    return 0;
}
```

**Output:**
```
Enter page size (in bytes): 4
Enter process size (in bytes): 13
Enter number of frames in physical memory: 8
Enter frame numbers for each page:
  Page 0 -> Frame: 2
  Page 1 -> Frame: 5
  Page 2 -> Frame: 1
  Page 3 -> Frame: 6

--- Page Table ---
Page No.        Frame No.
0               2
1               5
2               1
3               6

Enter logical address to translate: 10

Logical Address  : 10
Page Number      : 2
Offset           : 2
Physical Address : 6

Internal Fragmentation: 3 bytes
```

---

## Practical 11

**Aim:** Write a program to simulate the segmentation technique of memory management.

**Software Requirement:** Visual Studio Code

**Language Used:** C

**Theory:**
**Segmentation** divides a process into variable-sized segments based on the logical divisions of the program (e.g., code, stack, data). A **Segment Table** stores the base address and limit (length) of each segment.

Key formulas:
- **Physical Address** = Base Address + Offset
- **Condition**: Offset must be less than Limit (Segment Length), else **Segmentation Fault**

Unlike paging, segmentation has no internal fragmentation, but may cause external fragmentation.

**Steps:**
Open VS Code → Create `segmentation.c` file → Write the program → Save the program → Run the program by clicking on the play button at the top of the screen → Get the output

**Program:**

```c
#include <stdio.h>

int main() {
    int n, i;
    printf("Enter number of segments: ");
    scanf("%d", &n);

    int base[n], limit[n];
    printf("Enter base address and limit for each segment:\n");
    for (i = 0; i < n; i++) {
        printf("  Segment %d -> Base: ", i);
        scanf("%d", &base[i]);
        printf("  Segment %d -> Limit: ", i);
        scanf("%d", &limit[i]);
    }

    printf("\n--- Segment Table ---\n");
    printf("Seg No.\tBase\tLimit\n");
    for (i = 0; i < n; i++)
        printf("%d\t%d\t%d\n", i, base[i], limit[i]);

    int segNum, offset;
    printf("\nEnter segment number and offset to translate: ");
    scanf("%d %d", &segNum, &offset);

    if (segNum >= n) {
        printf("Error: Segment number out of range!\n");
    } else if (offset >= limit[segNum]) {
        printf("Segmentation Fault! Offset exceeds segment limit.\n");
    } else {
        int physAddr = base[segNum] + offset;
        printf("\nSegment Number   : %d", segNum);
        printf("\nOffset           : %d", offset);
        printf("\nBase Address     : %d", base[segNum]);
        printf("\nPhysical Address : %d\n", physAddr);
    }

    return 0;
}
```

**Output:**
```
Enter number of segments: 3
Enter base address and limit for each segment:
  Segment 0 -> Base: 1000
  Segment 0 -> Limit: 400
  Segment 1 -> Base: 2000
  Segment 1 -> Limit: 300
  Segment 2 -> Base: 3000
  Segment 2 -> Limit: 500

--- Segment Table ---
Seg No. Base    Limit
0       1000    400
1       2000    300
2       3000    500

Enter segment number and offset to translate: 1 150

Segment Number   : 1
Offset           : 150
Base Address     : 2000
Physical Address : 2150
```

---

