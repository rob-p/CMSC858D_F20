---
type: assignment
date: 2020-10-06
title: 'Homework 1'
#pdf: /static_files/assignments/asg.pdf
#attachment: /static_files/assignments/asg.zip
#solutions: /static_files/assignments/asg_solutions.pdf
due: 2020-10-22
---

# CMSC858D Homework 1 : Implementing some succinct primitives

This homework consists of 3 programming tasks.  The programming tasks build upon each other, and so should be implemented **in order**.  Each programming tasks consists of (1) an implementation and (2) a writeup.  Each implementation should be done in a separate directory, along with a README containing instructions for building (if necessary) and using the implementation (e.g. a description of the library interface, and the command-line interace if it exists).  In addition to the code you submit directly, you should also make a GitHub repository to which you post the code, and you should link to this in the README.

The writeups for all three tasks should be put into a single document (either a PDF or a Word Document).  Each writeup should include the following **3** components:
    
1. A short (no more than a paragraph) prose description of your implementation.

2. A brief (again, no more than a paragraph) summary of what you found to be the most difficult part of implementing each task.

3. The plots as described in each task.

## Programming Tasks

**Note** : For the programming tasks of this assignment, there is not a specific language requirement you have to follow.  However, the tasks should be completed in a "lower-level" language that gives you direct access to memory management and bit-manipulation primitives.  Good candidates for implementing this project would be C, C++ (ideally modern variants such as C++11/14/17), or Rust.  Some other languages (managed memory languages) may be allowed, but please check with me first and please _mind the memory usage_, as the point of this project is to measure the overhead of these succinct data structures.  You may _not_ use a succinct data structure library to implement these tasks (the point is for you to implement them yourself).  However, you _may_ choose to use a library to provide the basic bit-vector representation and direct access operations if you wish (i.e. you may use the library to allow creation, population and direct access to a bit-vector, but not for rank, select, or any other "higher-level" operations).  Also, note that you will find implementations of rank and select all over the place (some even from the previous iteration of this course).  The point of the project is for you to implement these yourself, so please avoid the desire to comb through that existing code.

If you do end up deriving some piece of your code from a source you encounter (either code, or a paper), make sure to _cite_ this source in both your code and in the homework you turn in.

## Task 1 — bit-vector rank

 Implement a succinct, constant-time, bit-vector rank operation.  The exact design of the implementation is up to you, but the following requirements are put in place.  In addition to turning your code in on ELMS, you should upload your code to a GitHub repository and include the link in your submission.
 
 * Your rank support should be a class / structure that "wraps" the underlying bit-vector.  In C++ lingo, something like:

    ```
    bit_vector b;
    // do some stuff with b
    rank_support r(&b); // let r access b via a pointer
    auto x = r(i); // x now holds the value of rank1(b, i);
                   // I'm using r(i) (operator()) as a shorthand
                   // for rank1(.) as defined below.
    ```
    
  * You should implement the following methods for your rank_support class (type signatures are given for C++, use your judgement if implementing in another language):
      
      * `uint64_t rank1(uint64_t i)` : Returns the number of 1s in the underlying bit-vector up to position i (inclusive).
      * `uint64_t rank0(uint64_t i)` : Returns the number of 0s in the underlying bit-vector up to position i (inclusive) — simply `i - rank1(i)`.
      * `uint64_t overhead()` : Returns the size of the rank data structure (in _bits_) that is required to support constant-time rank on the current bitvector.
      * `save(string& fname)` : Saves the rank data structure for this bit vector to the file `fname` (your bit vector should also have a `save()` function).
      * `load(string& fname)` : Loads the rank data structure for this bit vector from the file `fname` (your bit vector should also have a `load()` function).

 **Writeup**: For this programming task, test your implementation by invoking it for bit vectors of various sizes, and plotting the bit-vector size (say N) versus the time requried to do some fixed number of rank operations.  Also, plot the bit-vector size (say N) versus the result of calling the `overhead()` function.  Does your implementation match the expected theoretical bounds?
 
 
## Task 2 — bit-vector select

 Implement a succinct, (at most) log-time, bit-vector select operation.  The exact design of the implementation is up to you, but the following requirements are put in place.  In addition to turning your code in on ELMS, you should upload your code to a GitHub repository and include the link in your submission.
 
 * Your select support should be a class / structure that "wraps" the underlying bit-vector.  In C++ lingo, something like:

    ```
    bit_vector b;
    // do some stuff with b
    rank_support r(&b); // let r access b via a pointer
    select_support s(&r); // let s access r via a pointer
    auto x = s(i); // x now holds the value of select1(b, i);
                   // I'm using r(i) (operator()) as a shorthand
                   // for select1(.) as defined below.
    ```
    
  * You should implement the following methods for your rank_support class (type signatures are given for C++, use your judgement if implementing in another language):
      
      * `uint64_t select1(uint64_t i)` : Returns the position, in the underlying bit-vector, of the i<sup>th</sup> 1.
      * `uint64_t select0(uint64_t i)` : Returns the the position, in the underlying bit-vector, of the i<sup>th</sup> 0.
      * `uint64_t overhead()` : Returns the size of the select data structure (in _bits_) that is required to support log-time select on the current bitvector (how does this relate to the size of the `rank` data structure built above).
      * `save(string& fname)` : Saves the select data structure for this bit vector to the file `fname` (your bit vector should also have a `save()` function).
      * `load(string& fname)` : Loads the select data structure for this bit vector from the file `fname` (your bit vector should also have a `load()` function).


 **Writeup**: For this programming task, test your implementation by invoking it for bit vectors of various sizes, and plotting the bit-vector size (say N) versus the time requried to do some fixed number of select operations.  Also, plot the bit-vector size (say N) versus the result of calling the `overhead()` function.  Does your implementation match the expected theoretical bounds?  _If you feel ambitious_, you can additionally implement a constant-time bit-vector select, though this is not required.
 
 
 ## Task 3 — Bitvector-based occ table & backward search.

 Implement a bitvector-based occ table and a basic backward search over the FM-index of some text `T`.  For this task, you do _not_ need to implement the algorithm for performing the BWT 
 transform yourself (though you are free to if you wish).  Also, you will be implementing the "basic" backward search which only needs to support the `occurrence` query.  That is, 
 it only needs to return, for some query pattern `p`, the BWT rows that correspond to the occurences of `p`, *not* the positions where these occurrences appear in the original text. 

 You are free to use an existing library to implement the BWT itself.  For example, in C++, you could use [this](https://github.com/kurpicz/saca-bench/tree/master/libdivsufsort) library, 
 which is quite easy to call and very efficient.


 Your final implementation should be a **command-line** program with the following interace (below, `$` is the command-line prompt and the name of the program is `bwocc`).
 
  * Build a basic FM-index of some input text `T`.  This command reads the string in `<input file>`, constructs the BWT, as well as the bit-vector based occ table and your representation of the first column of the text, and saves the resulting structure to the file `<output file>`.  The program should also write two lines to standard out; the first line should contain the number of distinct input characters in the `<input string>` file (i.e. the size of the alphabet of the string the tree is constructed over) and the second line should contain the number of characters in the input string.  The command should be executed as follows:
  
     `$bwocc build <input string> <output file>`
 
 * Query an FM-index for a set of patterns: Load your previously built FM-index from file, and issue a series of queries on the index contained in another file.  Below, `<saved fmindex>` is the file saved as `<output file>` above, and `<query patterns>` is a file containing a newline-separated list of patterns to search. The program should output the query intervals (one per-line) corresponding to each pattern.  The output for each interval should be two columns, separated by a tab (`\t`), that represents the *half-open* interval `[s,e)` that lists the rows in the BWT that correspond to occurences of the query.  If the query _doesn't_ occur in the text, then your interval should have `s = e`.  The output intervals should be written, one per-line, to standard out:

      `$bwocc query <saved fmindex> <query patterns>`


 **Writeup**: For this programming task, test your implementation by running it on a number of reference texts of different sizes (I recommend downloading small viral / bacterial genomes from NCBI RefSeq).  For each reference, build the FM-index, and then draw a number of substrings randomly from the text (that should be found upon query) and create a number of random substrings (that are unlikely to be found upon query).  Try generating separate query sets of different sizes — I suggest files with 1,000, 10,000, 100,000, and 1,000,000 different query patterns.  Try plotting how the total query time scales as a factor of (1) the number of query patterns and (2) the number of "present" vs. "absent" queries and (3) the length of the reference string that was indexed.  How do your plots compare to expectation? *Note*: for the purpose of making these plots, you need not use the command-line interace described above; you could instrument your code to output timing information in a benchmark mode rather than the query results.