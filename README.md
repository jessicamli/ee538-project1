# EE538: Project 1: Opinion Diffusion using Linked Lists

## Overview
In this project, you will simulate how opinions spread in a simple "society" of voters. Each voter holds one of two opinions — represented by 0 or 1. Voters influence one another: at each step, to fit into their social circle, voters adopt the majority opinion of their friends. This is repeated multiple times until no one is changing their opinions anymore or enough time (number of iterations) has passed.


## Data Representation
- The startercode loads the inputs from files in your workspace: opinions.txt and edge_list.txt.
opinions,txt contains a list of initial opinions. The file looks like

0, 0

1, 1

2, 1

...

Each line corresponding to a voter -- a line 2, 1 means voter 2 has opinion 1.

The other file "edge_list.txt" contains a pair of indices denoting who is influenced by whom. The file looks like

0, 2

2, 3

1, 5

...

0, 2 corresponds to: voter 0 influences voter 2.

## Sample Output
The program should print the state of the system at regular intervals. For example:

Iteration 0: fraction of 1's = 0.52

Iteration 100: fraction of 1's = 0.47

Iteration 200: fraction of 1's = 0.38

Consensus reached: all 0’s

## Part 1
Your task is to clone this repo and complete the startercode and complete the following:

```
void build_adj_matrix()
{
    // (1) allocate matrix adj of appropriate size


    // (2) run through edge list and populate adj
}

double calculate_fraction_of_ones()
{
    // (3) Calculate the fraction of nodes with opinion 1 and return it.
}

// For a given node, count majority opinion among its neighbours. Tie -> 0.
int get_majority_friend_opinions(int node)
{
    // (4) Count the number of neighbours with opinion 0 and opinion 1. Return the majority (0 or 1). 
    //If tie, return 0.
}

// Calculate new opinions for all voters and return if anyone's opinion changed
bool update_opinions()
{
    // (5) For each node, calculate the majority opinion among its neighbours and update the node's opinion.
    // Return true if any node's opinion changed, false otherwise.
    
}
```

Then, in main():

```
 cout << "Iteration " << iteration << ": fraction of 1's = " 
         << calculate_fraction_of_ones() << endl;
    
    // (6) Run until consensus or max iterations
    //while( ... )
    
    // Print final result
    double final_fraction = calculate_fraction_of_ones();
    cout << "Iteration " << iteration << ": fraction of 1's = " 
         << final_fraction << endl;
```

Test your code by editing the input .txt files.

## Part 2
For this part, we are asked to update the code to make it more efficient by using better data structures, thinking about time and space complexity in particular. 

My thought process for approaching this task was to first identify what was lowering the efficiency. From the Part 1 code, I noticed that the graph is stored as a full adjacency matrix (which requires O(N^2) space I believe) and every neighbor lookup looks at all nodes (which requires O(N) time I believe). For a larger network, this is not efficient.

As allowed by the Project 1.2 instructions, I asked AI (ChatGPT) "how to design graph data structures for large social networks in C++". It recommended that I implement an "incoming adjacency" since this "is valuable when your queries naturally go 'backwards' along edges." It cited typical cases like follower/follow graphs, ranking/recommendation algorithms, and efficient deletions. It also noted that it is not useful when graphs are undirected. However, since direction matters in this project (whose opinion influences anothers), and this project fits into the typical cases described, I decided to continue with this recommendation.  More specifically, I replaced the adjacency matrix with an adjacency list, which stores a list of nodes that influence each node. This change reduces the space complexity from O(N^2), where N is the number of nodes, to O(N+E), where E is the number of edges. Lookup time also reduced from O(N) to O(number of people influencing that node).

I also improved the calculation of fraction of nodes. In Part 1, this required iterating over all nodes. However, in Part 2, I implemented ones_count, which tracks the number of nodes with opinion 1 and is updated during each iteration, thus allowing the fraction to be computed in O(1) time.

Note: The instructions for Part 2 did not detail which sections of code we were allowed to change (like it had outlined in Part 1) so I assumed that I had free-range in changing whatever section of code made sense for me.

Testing the code resulted in an output of:

Total nodes: 40
Iteration 0: fraction of 1's = 0.525
Iteration 1: fraction of 1's = 0.45
Iteration 2: fraction of 1's = 0.375
Iteration 3: fraction of 1's = 0.3
Iteration 4: fraction of 1's = 0.1
Iteration 5: fraction of 1's = 0
Consensus reached: all 0's

This is identical to the output achieved in Part 1, which is desired. To confidently confirm whether this works on larger datasets, I could have ran this with much larger txt input files and checked the runtime with <chrono>. However, since the Project 1.2 instructions did not detail this, I assumed I would not have to. I also was not entirely confident in my ability to create such large input files manually (since I believe AI would not be allowed in generating this either) and, thus, I simply considered time and space complexity through observation of my modified code, instead of actual implementation.  
