This program, `scheduler.py`, allows you to see how different schedulers perform under scheduling metrics such as response time, turnaround time, and total wait time. See the README for details.
# Simulation (8/8) ⭐
## Roadmap  ✅
* [x] Question 1 ✅
* [x] Question 2 ✅
* [x] Question 3 ✅
* [x] Question 4 ✅
* [x] Question 5 ✅
* [x] Question 6 ✅
* [x] Question 7 ✅
## Question 1
Compute the response time and turnaround time when running three jobs of length 200 with the SJF and FIFO schedulers.
### Solution
Invoke the following commands:
```
./scheduler.py --jlist=200,200,200 --policy=SFJ
./scheduler.py --jlist=200,200,200 --policy=FIFO
```
## Question 2
Now do the same but with jobs of different length: 100, 200, and 300.
### Solution
Invoke the following commands:
```
./scheduler.py --jlist=100,200,300 --policy=SJF
./scheduler.py --jlist=100,200,300 --policy=FIFO
```
Those are the same because the queueing order is equal to the size order, this problem would be more interesting if we look at 
```
./scheduler.py --jlist=300,200,100 --policy=SJF
./scheduler.py --jlist=300,200,100 --policy=FIFO
```
instead.
## Question 3
Now do the same, but also with the RR scheduler and a time-slice of 1.
### Solution
Changing up the policy and now we have to add a time slice in form of the quantum ArgV.
```
./scheduler.py --jlist=100,200,300 --policy=RR --quantum=1
```
## Question 4
For what types of workloads does SJF deliver the same turnaround times as FIFO?
### Solution
When, as in Question 1 and 2, the time order of the jobs is equal to the queue order, i.e., if the job list times are monotonically increasing.
## Question 5
For what types of workloads and quantum length does SJF deliver the same response times as RR?
### Solution
When the time slice is longer or equal to the job runtimes.
## Question 6
What happens to response time with SJF as job lengths increase? Can you use the simulator to demonstrate the trend?
### Solution
If all jobs increase in time by the same amount $\Delta t$ then pushes up all response times and therefore also the average by this factor.
## Question 7
What happens to response time with RR as quantum lengths increase? Can you write an equation that gives the worst-case response time, given N jobs?
### Solution
We need to wait one quantum per job, so the Nth job is first ran at time
$$
(N - 1) \cdot \text{quantum}
$$
therefore this is our worst-case response time.