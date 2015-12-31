title: Greedy algorithm
date: 2015-12-09 03:22:03
description:
categories: Algorithm
tags:
- Greedy
---
Greedy algorithm is a common method in designing algorithm. It is more like a heuristic method instead of a algorithm. Every time multiple choices are faced, the program make an optimal choice (the choice seems best), so hopefully the final result will also be optimal. Unlike exhaustive dynamic programming  which examines all possibilities, it only concerns the "best choice" and the following consequeunce, and other possible choices will not be re-considered afterward.

# The fractional knapsack problem
Given items `I1, V2,...Vn` with value `V1, V2.,.Vn` and weight `W1, W2...Wn`
And a bag with weight `W`
Find the maximum sum of values of items can carry with the bag if the items can be divided in fraction.

## Solution
<pre class="sample">
Calculte the value/weight of each item, `V1/W1, V2/W2, ...Vn/Wn`
Extract the item with highest value/weight
   If item can be hold fully into the bag
      Put it in the bag fully
   else 
      Divide the item into fractional amount to fit the remaining space in the bag
</pre>

Example:
W = 4


|  Item # | 1      | 2      | 3       | 4       | 
|---------|-----|
|  (W, V) | (1, 5) | (2, 9) | (3, 12) | (3, 11) |
|  V/ W   |   5     |4.5    |4      | 3.67      |

1. Extrat Item 1, V = 0 + 5, Remaing W = 4 - 1 = 3
2. Extract Item 2, V = 5 + 9 = 14, Remaining W = 3 - 2 = 1
3. Extract Item 3, V = 14 + (Remaining W / W3) V3 = 1/3 * 12 = 18

## Proof
Item n has the highest value/weight, 
Item a and b have lower value/weight

<pre class="sample">
Given 
1. Wa + Wb = Wn
2. Vn/Wn < Va/Wa 
3. Vn/Wn < Vb/Wb

Wn * (Vn / Wn) # Value provided by highest v/w item
= (Wa + Wb) * (Vn / Wn)
= (Wa) * (Vn / Wn) + (Wb) * (Vn / Wn)
> (Wa) * (Va / Wa) + (Wb) * (Vb / Wb) # Value provided by other lower v/w items with equal weight
Therefore, Vn > Va + Vb
</pre>

# Interval Sceduling
Slect job from a set of jobs (Start, End): (0, 10), (3, 4), (2, 8), (1, 5), (4, 5), (4, 8), (5, 6) (7,9)
so that maximum the amount of job cna be done in a period.

Running time: O(n log n)

## Solution
<pre class="sample">
Sort them in by finish time so that J1, J2, ...Jn
A = set of selected job
for i = 1 to n
  if (Ji is compatible with A)
    A union Ji

Running time complexity: O(n log n)
</pre>

## Proof
<pre class="sample">
C = COUNTER EXAMPLE

Not by start time
CCCCCCCCCCCCCCCCCC
  AAAAAA BBBBB

Not by shortest interval
AAAAAAA AAAAAA
      CCCC
</pre>

Proved by "greedy stays head" method, directly compare the greedy algorithm we purposed is not worse than other possible optimal solution in some measure.
Let `I1, I2 ... In` be the set of job selected by greedy algorithm
Let `J1, J2 ... Jn` be the set of job selected by other possible optimal solution
If we can show that `f(In) <= f(Jn)` for all n, the greedy algorithm is considered optimal too, in term of amount of job, the greedy algoritm produces at least same amount of job as the optimal solution get
**BY INDUCTION:** 
When n holds, n+1 also hold (based on n), it holds for all n

Basis: For r-th job, as we select min finish time
`f(Ir) <= f(Jr)`

Inductive step: 
`f(Ir) <= f(Jr) <= s(Jr+1) <= f(Jr+1)`
`f(Ir) <= s(Ir+1) <= f(Ir+1)`

For job `r+1`, the greedy algorithm selects min finish among other possible choice (Job available after f(Ir)), other optimal solutions select from choices after f(Jr)
Therefore, `f(Ir+1) <= f(Jr+1)`
`f(Jr + 1)` can be only later or equal to `f(Ir+1)`

# Interval Partitioning
Given a set of classes with start time and end time:
Allocate room for classes so that minimum rooms are required

Running time: O(n log n), store the class finished time in min-heap

## Solution
<pre class="sample">
  Sort the classes by start time c1, c2, ... cn
  d = no. of room needed
  for i = 1 to n
    if (ci is compatible with room k)
       put ci in room k
    else
       allocate a new room d + 1
       put ci in the new room d + 1
       d = d + 1
</pre>


## Proof
**Exploit of structural property**
Examine what is the property of an feasible solution and optimal solution, prove the greedy algorithm has that property of optimal solution.

A feasible solution could be : Number of classroom needed >= depth
where depth means the maximum number of set of intervals with no collision that contain at any given time
Therefore, Optimal(Best) solution: Number of classroom needed = depth (1)
At any time, at most one class can be assigned to a room (2)
Let say depth is 3, and the classroom allocated is also 3, it fits the property so it is the optimal solution. 

Questions is : how do we prove that classroom need = depth
1. If d classroom is just opened for job j, it means class j is not compatible with d - 1 classes
2. Since the classes are sorted in start time by ascending order, d -1 classes start no later than S(j) (Based on 2)
3. At any time: S(j) + very small amount of time, there will be d classes overlapping at that time, therefore, depth = d 
4. According to (1), it is the optimal solution.

# Minimizing Lateness
Given a set of Job J1, J2, ...Jn with durations T1, T2 ... T3 and due time D1, D2...Dn
Lateness is defined as max(0, Fi - Dn) where Fi is the finished time of i-th job  
Minimizing the maximum lateness. 

## Solution
Sort the jobs with deadline in ascending order
let t = current time
for i = 1 to n
  Si = t
  Fi = t + Ti
  t = Fi
lateness = max(lateness, max(0, Di - Fi))

## Proof
**Exchange argument**
Indirectly prove our solution is not worse than other, so it is a optimal solution. 
It is done by transforming other possible solution to our solution without hurting the solution quality.

1. Other solutions may have idle time, our solution have no idle time
=> No idle time will decrease the max lateness (GOOD!!)
2. Other solutions may have inversion of jobs i < j but Dj < Di, other solution have no inversion as we sort by deadline in ascending order
=> Let swap them try try, maximum lateness will not decrease after swap

<pre class="sample">
Before swap
       Fi    Fj 
[i      ][j   ]

After swap
    Fj'     Fi' 
[j   ][i      ]
</pre>


Only moving later of the finished time of i will increase the lateness
because for j: Fj' < Fj so max(0, Fj' - Dj) < max(0, Fj - Dj) 

For i:
lateness of job i before swap = `max(0, Fi - Di)   (1)`
<
lateness of job i after swap  = `max(0, Fi' - Di)` = `max(0, Fj - Di)   <= (2) because Dj < Di` 
The lateness of Job i after swap will not bigger than the original lateness of job j



# Limimitation 
- The final result is not always guaranteed the best, other choices may produce a better final result. (0-1 Knapsack problem) However, when the best answer is not required (approximate answer), greedy algorithm can be a good choice.
- Sometime is hard decide which is the "best choice"