# Chapter 4: The Abstraction: The Process
This program, `process-run.py`, allows you to see how process states changes as programs run and either use the CPU (e.g., perform an add instruction) or do I/O (e.g., send a request to a disk and wait for it to complete). See the README for details.
## Roadmap ✅
* Question 1 ✅
* Question 2 ✅
* Question 3 ✅
* Question 4 ✅
* Question 5 ✅
* Question 6 ✅
* Question 7 ✅
* Question 8 ✅
## Question 1 ✅
Run `process-run.py` with the following flags: `-l 5:100,5:100`. What should the CPU utilization be (e.g., the percent of time the CPU is in use?) Why do you know this? Use the `-c` and `-p` falgs to see if you were right.
### Solution
First we invoke
```
python3 cpu-intro/process-run.py -h
```
to see what the `-l` flag does:
```
  -l PROCESS_LIST, --processlist=PROCESS_LIST
                        a comma-separated list of processes to run, in the
                        form X1:Y1,X2:Y2,... where X is the number of
                        instructions that process should run, and Y the
                        chances (from 0 to 100) that an instruction will use
                        the CPU or issue an IO (i.e., if Y is 100, a process
                        will ONLY use the CPU and issue no I/Os; if Y is 0, a
                        process will only issue I/Os)
```
We are invoking
```
python3 cpu-intro/process-run.py -l 5:100,5:100
```
so `X1:Y1 == 5:100` meaning that we are running 5 processes and there is a 100% chance that we use the CPU, rather than issueing a IO, same for `X2:Y2 == 5:100`, as such I expect the CPU utilization to be 100%.

We invoke
```
python3 '/Users/danielsinkin/GitHub_private/ostep-homework/cpu-intro/process-run.py' -l 5:100,5:100 -c   
```
and get
```
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2        RUN:cpu         READY             1          
  3        RUN:cpu         READY             1          
  4        RUN:cpu         READY             1          
  5        RUN:cpu         READY             1          
  6           DONE       RUN:cpu             1          
  7           DONE       RUN:cpu             1          
  8           DONE       RUN:cpu             1          
  9           DONE       RUN:cpu             1          
 10           DONE       RUN:cpu             1
```
as such we first finish running the entirety of the first process and then the entirety of the second process, and the CPU utilization is 100%, which we can verify by adding the `-p` flag at the end:
```
Stats: Total Time 10
Stats: CPU Busy 10 (100.00%)
Stats: IO Busy  0 (0.00%)
```
## Question 2 ✅
Now run with these flags: `./process-run.py -l 4:100,1:0`. These flags specify one process with 4 instructions (all to use the CPU), and one that simply issues an I/O and waits for it to be done. How long does it take to complete both processes? Use `-c` and `-p` to find out if you were right.
### Solution
I would assume that one of two things would happen, either the OS scheduler does not see that we are interested in doing in IO call in the second process because running through the entirety of the first process or it does see it and does it first. In the first case we'll run for 5 CPU steps and in the second case we'd run for 4 steps.

Before running, I think it is the latter case, so we'll have 4 timesteps until we are done.

After running the simulation we can see:
```
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1          
  2        RUN:cpu         READY             1          
  3        RUN:cpu         READY             1          
  4        RUN:cpu         READY             1          
  5           DONE        RUN:io             1          
  6           DONE       BLOCKED                           1
  7           DONE       BLOCKED                           1
  8           DONE       BLOCKED                           1
  9           DONE       BLOCKED                           1
 10           DONE       BLOCKED                           1
 11*          DONE   RUN:io_done             1  
```
So we I wrong, the IO does not get intelligently scheduled (which makes sense given that there is no scheduler), and  I made the wrong assumption that the IO would only take one timestep, it is a passable parater with default value of 5, although this is not documented.

But after reflecting on it this makese sense, we have our first process which runs for 4 steps, then the second process initializes the IO with one step, IO takes 5 steps, then the IO gets finished in one step so in total we have a runtime of
```
CPU1 = 4
CPU2 = 1 + 5 + 1 = 7
CPU1 + CPU2 = 11.
```
## Question 3 ✅
Switch the order of the processes: `-l 1:0,4:100`. What happens now? Does switching the order matter? Why?
### Solution
I would assume that we will have the following schematic run:
```
CPU1: RUN:io , CPU2: READY
CPU1: BLOCKED, CPU2: RUN:cpu
CPU1: BLOCKED, CPU2: RUN:cpu
CPU1: BLOCKED, CPU2: RUN:cpu
CPU1: BLOCKED, CPU2: RUN:cpu
CPU1: BLOCKED, CPU2: DONE
CPU1: RUN:io , CPU2: DONE
```
So that we 7 iterations in total.

My prediction was exactly right, the reason why this works is that the second CPU can do computations while the first one is blocked, in the previous task we were just waiting for the IO block to be resolved.
## Question 4 ✅
We'll now explore some of the other flags. ONe important flag is `-S`, which determines how the system react when a process issues an I/O. With the flag set to `SWITCH_ON_END`, the system will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two rpocesses (`-l 1:0,4:100 -c -S SWITCH_ON_END`), one doing I/O and the other doing CPU?
### Solution
I would assume that the IO is blocking, so we just do the entire IO setup and will end up with 11 steps just like in the second question.

My prediction was correct:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED         READY                           1
  3        BLOCKED         READY                           1
  4        BLOCKED         READY                           1
  5        BLOCKED         READY                           1
  6        BLOCKED         READY                           1
  7*   RUN:io_done         READY             1          
  8           DONE       RUN:cpu             1          
  9           DONE       RUN:cpu             1          
 10           DONE       RUN:cpu             1          
 11           DONE       RUN:cpu             1
```
## Question 5 ✅
Now, run the same processes, but with one switching behaviour set to switch to another process whenever one is WAITING for I/O (`-l 1:0,4:100 -c -S SWITCH_ON_IO`). What happens now?
### Solution
I would assume we get exactly the default behaviour as in Question 3.

I was correct:
```
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1          
  2        BLOCKED       RUN:cpu             1             1
  3        BLOCKED       RUN:cpu             1             1
  4        BLOCKED       RUN:cpu             1             1
  5        BLOCKED       RUN:cpu             1             1
  6        BLOCKED          DONE                           1
  7*   RUN:io_done          DONE             1
```
## Question 6 ✅
One other important behavior is what to do when an I/O completes. With `-I IO_RUN_LATER`, when an I/O completes, the process that issues it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when you run this combination of processes? (`-l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_LATER`)
### Solution
I Would assume that the IO starts running and then process 2, 3, 4 are all completed before we loop back to Process one.

My prediction was correct:
```
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUN:cpu         READY         READY             1             1
  3        BLOCKED       RUN:cpu         READY         READY             1             1
  4        BLOCKED       RUN:cpu         READY         READY             1             1
  5        BLOCKED       RUN:cpu         READY         READY             1             1
  6        BLOCKED       RUN:cpu         READY         READY             1             1
  7*         READY          DONE       RUN:cpu         READY             1          
  8          READY          DONE       RUN:cpu         READY             1          
  9          READY          DONE       RUN:cpu         READY             1          
 10          READY          DONE       RUN:cpu         READY             1          
 11          READY          DONE       RUN:cpu         READY             1          
 12          READY          DONE          DONE       RUN:cpu             1          
 13          READY          DONE          DONE       RUN:cpu             1          
 14          READY          DONE          DONE       RUN:cpu             1          
 15          READY          DONE          DONE       RUN:cpu             1          
 16          READY          DONE          DONE       RUN:cpu             1          
 17    RUN:io_done          DONE          DONE          DONE             1
```
## Question 7 ✅
Now run the same processes, but with `-I IO_RUN_IMMEDIATE` set, which immediately runs the process that issued the I/O. How does this behavior differ? Why might running a process that just completed an I/O again be a good idea?

I would assume that we start the IO, then the second CPU does its entire run, then the first CPU finishes its IO and then we pass the tasks back.

The reason why we would like to immediately go back to the IO starting CPU is so we can process the IO stuff immediately, in particular do any necessary context closing and cleanup.

My prediction was correct:
```
Time        PID: 0        PID: 1        PID: 2        PID: 3           CPU           IOs
  1         RUN:io         READY         READY         READY             1          
  2        BLOCKED       RUN:cpu         READY         READY             1             1
  3        BLOCKED       RUN:cpu         READY         READY             1             1
  4        BLOCKED       RUN:cpu         READY         READY             1             1
  5        BLOCKED       RUN:cpu         READY         READY             1             1
  6        BLOCKED       RUN:cpu         READY         READY             1             1
  7*   RUN:io_done          DONE         READY         READY             1          
  8           DONE          DONE       RUN:cpu         READY             1          
  9           DONE          DONE       RUN:cpu         READY             1          
 10           DONE          DONE       RUN:cpu         READY             1          
 11           DONE          DONE       RUN:cpu         READY             1          
 12           DONE          DONE       RUN:cpu         READY             1          
 13           DONE          DONE          DONE       RUN:cpu             1          
 14           DONE          DONE          DONE       RUN:cpu             1          
 15           DONE          DONE          DONE       RUN:cpu             1          
 16           DONE          DONE          DONE       RUN:cpu             1          
 17           DONE          DONE          DONE       RUN:cpu             1
```
Question 8 ✅
Now run with some randomly generated processes using flags `-s 1 -l 3:50,3:50` or `-s 2 -l 3:50,3:50` or `-s 3 -l 3:50,3:50`. See if you can predict how the trace will turn out. What happens when you use the flag `-I IO_RUN_IMMEDIATE` versus that flag `-I IO_RUN_LATER`? What happens when you use the flag `-S SWITCH_ON_IO` versus `-S SWITCH_ON_END`.
### Solution
Ran the different permuations, all looks as expected.