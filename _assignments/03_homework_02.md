---
type: assignment
date: 2020-11-16
title: 'Homework 2'
#pdf: /static_files/assignments/asg.pdf
#attachment: /static_files/assignments/asg.zip
#solutions: /static_files/assignments/asg_solutions.pdf
due_event: 
    type: due
    date: 2020-12-01 23:59:00
    description: 'Homework #2 due'
---

**Due: Tues Dec 1, 2020 (11:59 PM)**  

# CMSC858D Homework 2 : Implementing a minimum perfect hash function

This homework is due by **11:59PM ET on Tues. Dec 1**.  It consists of 1 programming tasks with an associated writeup.  The programming task builds upon the rank and select primitives that you implemented last time.  **However, you need not use your previous implementation of a rank and select enabled bit-vector; you can use any existing implementation for this functionality (or roll your own)**.  The implementation should include a README containing instructions for building and using the implementation (e.g. a description of the library interface, and the command-line interace).  In addition to the code you submit directly, you should also make a GitHub repository to which you post the code, and you should link to this in the README.

The writeup should be put into a single document (either a PDF or a Word Document). The writeup should include the following **3** components:
    
1. A short (no more than a paragraph) prose description of your implementation.

2. A brief (again, no more than a paragraph) summary of what you found to be the most difficult part of implementing the project.

3. The plots as described below.

## Programming Task

**Note** : For the programming tasks of this assignment, there is not a specific language requirement you have to follow.  However, the task should be completed in a "lower-level" language that gives you direct access to memory management and bit-manipulation primitives.  Good candidates for implementing this project would be C, C++ (ideally modern variants such as C++11/14/17), or Rust.  Some other languages (managed memory languages) may be allowed, but please check with me first.  You may use a succinct data structure library to implement these tasks.

If you nd up deriving some piece of your code from a source you encounter (either code, or a paper), make sure to _cite_ this source in both your code and in the homework you turn in.  Note: **everyone** should cite the [BBHash paper](http://drops.dagstuhl.de/opus/volltexte/2017/7619/pdf/LIPIcs-SEA-2017-25.pdf) in their README.

## Implementing the BBHash algorithm

 Implement a single-threaded version of the BBHash algorithm we covered in class.  Recall that the BBHash algorithm worked by building a cascading set of bit vectors where,
 within each level, each 1 corresponds to a key whose hash takes it uniquely to the index of that 1 without collisions with any other key in the set.  Plese refer to the paper and slides for the implementation details of the hash table, but note that you will need to rely on (1) a rank-enabled bit vector implementation, (2) a hash function that can take, as input, a seed (I can recommend [XXHash](https://github.com/Cyan4973/xxHash)) and (3) a "regular" hash table at the final level of the algorithm.

 You should allow your bit vectors to cascade up to 3 levels before you put the remaining colliding elements into the terminal hash table.  Also, like the normal BBHash implementation, your implementation should take a \\(\gamma\\) parameter as input to trade off space for time (by lowering the collision probability).
 
 The exact design details of the implementation are up to you, but the following requirements are put in place.  In addition to turning your code in on ELMS, you should upload your code to a GitHub repository and include the link in your submission.

 * Your minimal perfect hash function should expose a minimal library interface with at least a constructor and a lookup function.
   * Your constructor should take a reference or pointer to the set of keys (perhaps stored in a `vector`), the number of keys `n`, and the \\(\gamma\\) parameter to use in construction.
   * Your lookup function should take a key and return a 64-bit unsigned integer.  If the key was in the universe on which the hash was built, this should be the unique value to which this key maps under the minmal perfect hash function; otherwise it can be any 64-bit value (possibly larger than `n`).
   * _Optionally_: You can implement save and load functions for your minimal perfect hash function to make it easier to do performance testing.
  
 **NOTE** : Ideally, your minimal perfect hash implementation should be a _template_ class, which can work with keys of any type. However, I realize that, despite its utility, template programming is not something with which I can easily assume wide familiarity.  Thus, while it's preferable to implement this class as a template (or a generic trait if you choose a language like Rust), it is acceptable to assume the key type will be a string.

 **Writeup**: For the writeup, you should test a number of properties of your hash function, and report the results along with your interpretation (if you find any of the results surprising).  You should measure 3 different aspects of your minimal perfect hash function.

 * Speed of access : You should construct both your minimal perfect hash and a standard hash map (e.g. `unordered_map` in C++ a map from keys to integers/indices) on inputs of various sizes and with various values of \\(\gamma\\), and measure how long, on average, it takes to look up elements.  I recommend trying sets of increasing sizes like `n=10000`, `n=100000`, `n=1000000`, and a few \\(\gamma\\) values for each, like the ones used in the paper.  Because a minimal perfect hash is not necessarily valid for keys on which it was not constructed (sometimes called "alien" keys), you should only test the lookup here on elements from the original key set on which you built each hash.  Provide a report of the average time to lookup keys in the minimal perfect hash compared to the standard hash for each value of `n` you test and for each value of \\(\gamma\\).

 * Size of data structure : You can do this measurement on the same set of mimimal perfect hash functions you built above to measure speed.  The exact measurement can be a little bit tricky here, so I recommend instead of trying to report the actual memory used, report the total number of bits present in the 3 levels of bit vectors, and separately the number of keys that need to be inserted in the terminal hash table.  How do these values change as you alter \\(\gamma\\)?  How do you think these sizes compare to if you'd put all of the keys in a standard hash map?

 * Rate of detection of alien keys : For each of the minimal perfect hash functions you constructed above, probe the hash with `1000` alien keys (i.e. keys that are not in the original key set).  For the hash functions at different values of `n` and \\(\gamma\\), report how often the lookup function returns a value that lets you _immediately_ determine that this key was not present in your original set.  That is, out of all `1000` queries in each hash table, how often does the lookup return a value `>=n`?  Is there a relationship between `n` and the rate of detecting alien keys?  Is there a relationship between \\(\gamma\\) and the rate of detecting alien keys?