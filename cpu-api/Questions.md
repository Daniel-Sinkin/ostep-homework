# Chapter 4: The Abstraction: The Process
This simulation homework focuses on `fork.py`, a simple proces creation simulator that shows how processes are related in a single "familial" tree. Read the relevant README for details about how to run the simulator.
## Roadmap
* Question 1 ✅
* Question 2 ✅
* Question 3 ✅
* Question 4 ✅
* Question 5 ✅
* Question 6 ✅
## Question 1 ✅
Run `./fork.py -s 10` and see which actions are taken. Can you predict what the process tree looks like at each step? Use the `-c` flag to check your answers. Try some different random seeds (`-s`) or add more actions (`-a`) to get he hand of it.
### Solution
Behaves as expected
## Question 2 ✅
One control the simulator gives you is the `fork_percentage`, controlled by the `-f` flag. The higher it is, the more likely the next action is a fork; the lower it is, the more likely the action is an exit. Run the simulator with a large number of actions (e.g., `-a 100`) and vary the `fork_percentage` form 0.1 to 0.9. What do you think the resulting final process tree will look like as the percentage changes? Check your answer with `-c`.
### Solution
As one would expect if we have a high forking factor then we get very deep nested forking, if we have a very low one then most forks already get closed before we can make recursive forks:
```
                               a
                               ├── b
                               │   ├── j
                               │   │   ├── m
                               │   │   │   ├── n
                               │   │   │   │   └── B
                               │   │   │   │       └── aE
                               │   │   │   ├── q
                               │   │   │   │   └── x
                               │   │   │   │       ├── U
                               │   │   │   │       │   ├── aj
                               │   │   │   │       │   └── al
                               │   │   │   │       │       └── ao
                               │   │   │   │       ├── ah
                               │   │   │   │       │   ├── ak
                               │   │   │   │       │   └── aD
                               │   │   │   │       └── an
                               │   │   │   └── D
                               │   │   │       └── ae
                               │   │   │           └── aK
                               │   │   ├── v
                               │   │   └── O
                               │   ├── k
                               │   │   └── aH
                               │   ├── N
                               │   │   ├── Z
                               │   │   │   └── aA
                               │   │   └── ac
                               │   │       ├── ad
                               │   │       │   ├── ag
                               │   │       │   │   └── aw
                               │   │       │   └── av
                               │   │       └── aC
                               │   ├── X
                               │   └── ay
                               ├── d
                               │   ├── e
                               │   │   ├── f
                               │   │   │   └── af
                               │   │   │       └── ar
                               │   │   ├── o
                               │   │   │   ├── V
                               │   │   │   │   └── aI
                               │   │   │   └── ai
                               │   │   │       ├── am
                               │   │   │       └── at
                               │   │   ├── L
                               │   │   │   └── T
                               │   │   └── aa
                               │   ├── R
                               │   └── Y
                               ├── l
                               ├── w
                               │   └── aF
                               │       └── aJ
                               ├── t
                               │   └── aq
                               ├── s
                               ├── u
                               ├── H
                               ├── W
                               │   ├── au
                               │   └── ax
                               ├── ab
                               ├── F
                               ├── y
                               │   └── aG
                               ├── I
                               ├── K
                               ├── M
                               ├── S
                               ├── P
                               ├── Q
                               ├── ap
                               ├── az
                               ├── as
```
and for low forking factor:
```
                               a
Action: a forks U
                               a
                               └── U
Action: U EXITS
                               a
Action: a forks V
                               a
                               └── V
Action: a forks W
                               a
                               ├── V
                               └── W
Action: W EXITS
                               a
                               └── V
Action: V EXITS
                               a
Action: a forks X
                               a
                               └── X
Action: X EXITS
                               a
Action: a forks Y
                               a
                               └── Y
Action: Y EXITS
                               a
```
## Question 3 ✅
Now, switch the output by using the `-t`flag (e.g., run `./fork.py -t`). Given a set of process trees, can you tell which actions were taken?
### Solution
Yeah just look at where a new one gets spawned and its direct parent is who forked it.
## Question 4 ✅
One interesting thing to note is what happens when a child exits; what happens to its children in the process tree? To study this, let's create a specific example: `./fork.py -A a+b,b+c,c+d,c+e,c-`. This example has process `a` create `b`, which in turn creates `c`, which then creates `d` and `e`. However, then, `c` exits. What do you think the process tree should look like after the exit? What if you use the `-R` flag? Learn more about what happens to orphaned processes on your own to add more context.
### Solution
If we do not use the `-R` falg then the orphaned processes get assigned to the root, otherwise they just get assigned to the parents of the killed process.
## Question 5 ✅
One last flag to explore is the `-F` flag, which skips intermediate steps and only asks to fill in the final process tree. Run `./fork.py -F` and see if you can write down the final tree by looking at the series of actions generated. Use different random seeds to try this a few times.
### Solution
Using this call to create the actions:
```
python3 cpu-api/fork.py -s 2782367485 -F -a 12
```
resulting in output
```
Action: a forks b
Action: a forks c
Action: c forks d
Action: a forks e
Action: a forks f
Action: d forks g
Action: f EXITS
Action: c forks h
Action: d forks i
Action: a forks j
Action: d forks k
Action: h forks l
```
and this call to validate result:
```
python3 cpu-api/fork.py -s 2782367485 -c -a 12 -F
                        Final Process Tree:
                               a
                               ├── b
                               ├── c
                               │   ├── d
                               │   │   ├── g
                               │   │   ├── i
                               │   │   └── k
                               │   └── h
                               │       └── l
                               ├── e
                               └── j
```
## Question 6 ✅
Finally, use both `-t` and `-F` together. This shows the final process tree, but then asks you to fill in the actions that took place. By looking at the tree, can you determine the exact actions that took place? In which cases can you tell? In which can't you tell? Try some different random seeds to delve into this question.
### Solution
We can uniquely determine it if there were no exits, but if there were we can't really tell, especially if we do not set the `-R` flag as we can't determine which finished process the orphaned forks belonged to.