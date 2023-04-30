Download Link: https://assignmentchef.com/product/solved-cs251-project-5-tour-guide-system
<br>
In project 5, you will be developing some tools for use by a Tourism company to help customers plan their trips and to identify which tourist spots are most important to the company. In the first part, you will implement a system that reads trip information and stores it. Next, you will implement a system to find the cheapest tickets for your customers. Finally, you will generate a list of the company’s most visited tourist places.




<h1>Part 1: Identification of connected components and separation edges (bridges)</h1>

<strong> </strong>

<strong>Part 1.1: Building the network  (10 points)</strong>




In this part you will read in the tourist places and the routes between them. Input will start with a number 1, representing part 1 of the project. You will be given the number of tourist places (n) and the connections among them (m), followed by m pairs of tourist places and the travel ticket price associated with them. Create a network (graph) from the given data and identify sets of connected places. Two places are connected if they have a path between them. You can assume that each of the connections represent a two-way route. That is, if there is a path from A to B, there is also a path from B to A at the exact same price. Here is a small example of the input modelled with 6 tourist spots and routes between them.
















Example 1:

1

6 7

<ul>

 <li>B 10</li>

 <li>C 20</li>

 <li>A 5</li>

</ul>

E D 6

D C 8

D F 8

F E 3




This information should be stored as a graph in the form of an ​<strong>adjacency list</strong> for your project; take a look at the class slides for suggestions on possible solutions.




<strong>Part 1.2: Identifying the critical routes (30 points)</strong>




In this part you will identify the critical routes and biconnected components in the network that you build in part 1.1.  Critical routes are analogous to separation edges (bridges) in a graph. A separation edge is an edge whose removal increases the number of connected components.




<strong>Note: You must implement an iterative version of DFS for identifying critical routes and connected components — so no recursion. (You need to convert the recursive implementation given in your textbook to an iterative version).</strong>




Your output to the file should be in the following format.




&lt;number of connected components in the graph&gt;

&lt;number of separation edges&gt;

&lt;separation edges listed in lexicographical order&gt;




i.e., in the last example D C is a separation edge. After removing it, we have two separate connected components: {A,B,C} and {D,E,F}. Therefore, the expected output is:

1

1

C D




Also refer to the sample test cases provided in the tests folder (Also make sure to output both the nodes of an edge using lexicographical order. For example in the above example your program should not output &lt;D C&gt;).




<strong>Part 1: Input:</strong>

The integer “1” followed by two integers n and m, representing the number of places and the number of routes respectively.

<strong>Part 1: Output:</strong>

&lt;Number of connected components in the graph&gt;

&lt;Number of separation edges&gt;

&lt;Separation edges listed in alphabetical order&gt;

Your program should work in O(m+n) time to not hit a time-out.




<strong>Hint: </strong>

<strong> </strong>

You can modify DFS to find the separation edges. To do that for each vertex v, define the following evaluation function. It can be calculated in a constant time after visiting all the children of v and possible ancestors through the back edges.

evaFun(v) = ​<strong>min</strong>​ ​<strong>{</strong>​ disc(v), min { evaFun(u) | (v , u) is a tree edge },

min { disc(w) | (v , w) is a back edge}

​<strong>} </strong>

in which disc(v) is the ​<em>discovery</em> ​<em>time</em> of vertex v. After that vertex v can be popped from the stack.

A tree edge (s, t) is a separation edge if and only if evaFun(t) &gt; disc(s).

<em>Explanation:</em> evaFun(v) represents the earliest discovery time of a node you can get to from v, by going forward along an arbitrary number of tree edges and at most one back edge. The formula can be explained as follows:

min { disc(v), // v can always reach itself, so evaFun(v) &lt;= disc(v)

min { evaFun(u) | (v, u) is a tree edge }, // If one of v’s children can get to x using this method,   then so can v by taking the same path after going along

(v, u)

min { disc(w) | (v, w) is a back edge } //the lowest disc(w) among all back edges (v,w).

}

If an edge (u, v) is a separating edge, then it is necessarily a tree edge, so we only need to test tree edges. What happens if we have a tree edge (u, v) where evaFun(v) &gt; disc(u)? Well, this means that it is impossible to reach u or an ancestor of u through a back edge of one of v’s descendants. This means that if (u, v) were removed, it would be impossible to reach u from a DFS started at v, so the number of connected components increases upon the removal, and therefore (u, v) is a separating edge. Each of these steps should be reversible, justifying the bidirectional statement:

A tree edge (u, v) is a separating edge &lt;=&gt; evaFun(v) &gt; disc(u).

<strong>Here is the recursive procedure (**You need to implement an iterative version of this**) </strong>

Procedure DFS(v)

For all u ​∈​ adj(v) // For each adjacent vertex of ‘v’

If(!visited[u])

{

Eval_func[v] = discovery_time[v];

//Do DFS on u                                                 eval_func[v] = min (eval_func[v], eval_func[u])

}

else(back_edge)

{

eval_func[v] = min(eval_func[v], discovery_time[u])

}




In the iterative version, whenever you encounter a vertex (i.e., <strong>v</strong>​ )​ , it should be pushed into the stack. Update the eval_func for the node as described above. If all the children of ​<strong>v </strong>​have been visited, then you should pop ​<strong>v </strong>​from the stack.

<strong>Example:</strong>

In this example given on page 1, assume DFS started from vertex D. The following picture shows the DFS tree in solid lines and the back edges in dashed lines. There are two numbers next to each vertex, the black one is the discovery time and the red one is the result of the evaluation function for that vertex. Here, the only tree edge whose discovery time of the parent is less than the evaluation function of the child is edge C D, so it is a separation edge of the given graph.

<strong> </strong>




<strong>Instructions for part1: </strong>

<ul>

 <li><strong>You should store the graph in adjacency list. </strong></li>

 <li><strong>Iteratively implement routines for Separation edges and Connected components (Recursive implementations will not be evaluated or considered). You can use library routines for stack operations. </strong></li>

 <li><strong>There should not be any new line character at the end of the output. </strong></li>

</ul>

<strong> </strong>

<h1>Computing shortest paths using Dijkstra’s Algorithm</h1>

<strong> </strong>

<strong>Part 2. Finding the cheapest ticket  (35 Points)</strong>




Now that you have read in data, you will need to find tickets for your customers. In this project someone might want to travel between two places that requires a connection. That is, they may need to travel multiple cities to get to the destination as a direct route may not be available. You need to implement a Dijkstra’s shortest path algorithm in a way that lets you search for the cheapest path between two different cities. For simplicity you can assume that there is only one cheapest route between two different cities.




Input/Output for this section will directly follow the network input and is formatted as follows:

&lt;Source&gt; &lt;Destination&gt;

e.g. IND DTW

You should continue reading in ticket queries until you read in the token “END”.

Your output should be in the following format:

&lt;source&gt; &lt;list of intermediate nodes&gt; &lt;destination&gt; &lt;cost up to 2 decimal places&gt;.

<strong>Part 2: Input:</strong>

<ol>

 <li>The integer “2” followed by two integers n and m, representing the number of places and the number of routes respectively.</li>

 <li>m lines in the format &lt;source&gt; &lt;destination&gt; &lt;cost&gt;</li>

 <li>1 or more lines containing ticket queries in the form &lt;source&gt; &lt;destination&gt;</li>

 <li>A line containing “END”.</li>

</ol>




<strong>Part 2: Output:</strong>

Each line is the optimal route between the source and destination in the format: &lt;source &gt; … &lt;intermediate nodes&gt; … &lt;destination&gt; &lt;total cost to 2 decimal places&gt; or the line “not possible” if it is not possible to reach the destination.

Number of lines is equal to the number of queries, and in the same order.

<strong>Instructions for part 2: </strong>

<ul>

 <li><strong>Use the adjacency list constructed in part 1 for part 2. </strong></li>

 <li><strong>You are allowed to use the library routines for priority queues. </strong></li>

 <li><strong>There should not be any new line character at the end of the output. </strong></li>

</ul>

<strong> </strong>

<h1>Computing the Spanning Tree and Euler Tour</h1>




<strong>Part 3: Going on a tour (25 Points)</strong>

In part 3 you will be constructing a network in the same way as in part 1. However, this time our objective will be to find a way for a passenger to visit every tourist place offered by the tourist company. It turns out that this problem, a variation of a famous problem known as the travelling salesman problem, cannot currently be solved perfectly in a reasonable amount of time. Therefore, you will be implementing a simplified version of a solution to this problem, rather than an optimal one.

Your solution will be found in two steps. First, given the graph, you will construct a minimum spanning tree using either Prim-Jarnik or Kruskal’s algorithm. The root of this tree (start point of the tour) will be provided in the input. Once you have the minimum spanning tree, you should do an Eulerian tour of the tree. An Euler tour on tree is a walk around the tree where each edge is visited exactly once, and the tour starts and ends at the same vertex. Euler tour in which we visit the nodes on left produces preorder traversal and when we visit the nodes on the right produces a postorder traversal of the tree. Eulerian tour of the tree can be implemented according to the following pseudocode:

Void eulerTour(Minimum_Spanning_Tree G):

Print root node

For each child of the root, in alphabetical order:       eulerTour(child)




<strong>Part 3: Input: </strong>

<ol>

 <li>The integer 3 followed by two integers ‘n’ and ‘m’ representing the number of tourist places and number of interconnections among them.</li>

 <li>‘m’ lines in the format of &lt;source&gt; &lt;destination&gt; &lt;cost&gt;.</li>

 <li>One line containing the tourist place that will be the root of MST.</li>

</ol>

<strong>Output: </strong>​The Eulerian traversal according to the given pseudocode, or the line “not possible” if it is not possible to visit all cities.

<strong>Instructions for part 3: </strong>

<ul>

 <li><strong>Use the adjacency list constructed in part 1 for part 3. </strong></li>

 <li><strong>You are allowed to use the library routines for priority queues. </strong></li>

 <li><strong>You can implement a recursive program for euler tour. </strong></li>

 <li><strong>While testing, the test cases will be provided such that the output of Prim’s/Kruskal’s algorithms are the same. </strong></li>

 <li><strong>There should not be any new line character at the end of the output. </strong></li>

</ul>

<strong>       </strong>

<strong>Sample Input / Output Format</strong> (All​ input/output for project 5 will go through file

IO​)




<table width="601">

 <tbody>

  <tr>

   <td width="362">13 3A   B 163.30B   C 238.30C   D 239.90</td>

   <td width="239">13A   BB   CC   D</td>

  </tr>

  <tr>

   <td width="362">23 3A   B 163.30B   C 238.30A C 239.90A BC AC BEND</td>

   <td width="239">A B 163.30C A 239.90C B 238.30</td>

  </tr>

  <tr>

   <td width="362">24 2A B 163.30C D 150.00A CEND </td>

   <td width="239">not possible</td>

  </tr>

  <tr>

   <td width="362">35 4IND ORD 163.30ORD DTW 238.30DCA DTW 150.00DTW LAX 300.00DTW</td>

   <td width="239">DTWDCALAXORDIND</td>

  </tr>

 </tbody>

</table>




<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong>NOTE:</strong>




Important:

<ol>

 <li>If your program does not compile, your grade will be 0 – no exceptions.</li>

 <li>If you don’t follow the submissions instructions, your grade will be 0 – no exceptions.</li>

 <li>Plagiarism and any other form of academic dishonesty will be graded with 0 points as definitive score for the project and will be reported at the correspondent office. We will use MOSS​ to check your solutions.​</li>

</ol>




<strong>C++ Instructions: </strong>




Compilation will be done using g++ and makefiles. Create a folder with your username. Put all the source code (should not have any sub-directories in your submission folder) as well as the Makefile that compiles your provided source code into an executable named “program”.




Ex: g++ -o program main.cpp stack.cpp circular_deque.cpp -std=c++11 ./program &lt;input file&gt; &lt;output_file&gt;




Your project must compile using the standard g++ compiler on data.cs.purdue.edu. For convenience, you are provided with a template Makefile and C++ source file.




<strong>Java Instructions: </strong>

<strong> </strong>

<ul>

 <li>Compiler we use: javac (v10.0.2)</li>

 <li>Files to submit: Create a folder with your username. Put all java files into the folder. There should not be any sub-directories. Your main program must be named Main.java. We will run that one only. Your project must compile using the javac compiler (v10.0.2) on data.cs.purdue.edu.</li>

</ul>




Compiling process:

<ul>

 <li>To compile Main:</li>

</ul>

<ol>

 <li>Go to the project root.</li>

 <li>Type command in console: javac src/*.java</li>

</ol>




<ul>

 <li>To run Main:</li>

</ul>

<ol>

 <li>Go to the project root.</li>

 <li>Type command in console: java -cp src/Main inputfiles/test1.txt outputfiles/output-test1.txt</li>

</ol>


