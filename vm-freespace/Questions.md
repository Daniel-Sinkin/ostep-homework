The program, `malloc.py`, lets you explore the behaviour of a simple free-space allocator as described in the chapter. See the README for details of its basic operation.
# Simulation (3/7)
## Roadmap
* [x] Question 1 ✅
* [x] Question 2 ✅
* [x] Question 3 ✅
* [ ] Question 4
* [ ] Question 5
* [ ] Question 6
* [ ] Question 7
## Question 1 ✅
First run with the flags `-n 10 -H 0 -p BEST -s 0` to generate a few random allocations and frees. Can you predict what `alloc()`/`free()` will return? Can you guess the state of the free list after each request? What do you notice about the free list over time?
### Solution
Predicting how it behaves is pretty straightforward, search through all available chunks, looking for the one that is the smallest while still fitting our allocation goal and split that one up into an allocated part and a remaining part (unless it has exactly the allocated size).

The free list becomes more and more fragmented and longer with each `free()` invocation because we have no coalescing. In particular each free operation adds another entry to the free list.
## Question 2 ✅
How are the results different when using a WORST fit policy to search the free list (`-p WORST`)? What changes?
### Solution
We instead look for the largest possible chunk and split that one, what happens is that our free chunks are larger.
## Question 3 ✅
What about suing FIRST fit (`-p FIRST`)? What speeds up when you use first fit?
### Solution
With the FIRST fit we iterate through the free list until we find any fitting chunk and use that one, this avoids having to iterate through the entire list, like we'd do with WORST and BEST fit.
## Question 4
For the above questions, how the list os kept ordered can affect the time it takes to find a free location for some of the policies. Use the different free list orderings (`-l ADDRSORT`, `-l SIZEOSRT+`, `-l SISZESORT-`) to see how the policies and the list orderings interact.
### Solution
## Question 5
Coalescing of a free list can be quite important. Increase the number of random allocations (say to `-n 1000`). What happens to larger allocation requests over time? Run with and without coalescing (i.e., without and with the `-C` flag). What differences in outcome do you see? How big is the free list over time in each case? Does the ordering of the list matter in this case?
### Solution
## Question 6
What happens when you change the percent allocated fraction `-P` to higher than 50? What happens to allocations as it nears 100? What about as the percent nears 0?
### Solution
## Question 7
What kind of specific requests can you make to generate a highly-fragmented free space? Use the `-A` flag to create fragmented free lists, and see how different policies and options change the organisation of the free list.
### Solution