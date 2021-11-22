
[Milestone Report Link](milestone___report.pdf)

[Proposal Link](PROPOSAL.md)

## Summary

The current implementation of parallel fuzzing in AFL is only limited to synchronizing the seeds over multiple independent instances of AFL. Some number of papers exist on improving the parallelization of AFL; we are going to implement one such improvement, based on [this paper](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8668503). The aim of the project is to achieve similar if not better performance described in the paper.

## Background

Fuzzing is a testing method where a randomly mutated program input is generated as test cases to find abnormal behaviors, i.e. program crashes. A purely randomized input is necessarily inefficient, as most programs require an input of a specific structure and have checks for them; as such, various fuzzing techniques exist. One such technique is coverage-based fuzz testing, which collects coverage information and uses it to guide seed selection and mutation.

American Fuzzy Lop (AFL) is a coverage-based fuzzer; it captures basic block transitions and their hitcounts into a bitmap with a lightweight, compile-time instrumentation. Then, it uses a genetic algorithm to mutate the seeds; a test cases that triggers new paths in the program is marked as interesting and is used as a seed for further expolration; other less interesting seeds are discarded to reduce unnecessary testing.

Algorithm of AFL's fuzzing code is shown below, taken from the same paper above:

> __Input__: Initial seeds _Seeds_, Target program _P_  
> __Result__: Malicious Inputs  
> __for__ _seeds_ ∈ _Seeds_ __do__  
>> __for__ _iterations_ ← _0_ ___to___ _limit_ __do__  
>>> _input_ ← _seed_  
>>> _length_ ← len(_seed_)  
>>> _mutations ← RandInt(_length_)  
>>> __for__ _mut_ ← _0_ ___to___ _mutations_ __do__  
>>>> _byte_ ← RandInt(_length_)  
>>>> mutate(_input_, _byte_)  
>>>
>>> __end__  
>>> _result_, _cov_ ← Execute(_P_, _input_)  
>>> __if__ _result is crash_ __then__  
>>>> Append _input_ to _MaliciousInputs_
>>>
>>> __end__  
>>> __if__ _HasInputGain_(_cov_) __then__
>>>> Append _input_ to _Seeds_
>>>
>>> __end__  
>>
>> __end__  
>
> __end__

AFL has a parallel mode that makes it possible to run multiple instances of AFL on a multi-core system. Throughout the test, the instances will run independently, synchronizing any interesting seeds that were discovered and coverage information at certain intervals. Such mode of parallelization, however, does not deal with task splitting and reallocation, causing multiple instances to go over the same subspace and therefore is inefficient.

## The Challenge

The main challenges of the project are: \
Given only an unknown binary file,
1. There is no deterministic way of knowing the runtime of a program instance given inputs generated from a particular seed
2. There is no way to construct a tree of the program space
3. Synchronization across multiple processors is a pain given the seed prioritization scheme that aims to explore subspaces not previously explored by other processors

To tackle the challenges above: 
1. Improve resilience using the current state information and timeout parameters
2. Provide a estimate of the size of the program space from the reference afl implementation
3. Allocate local and global memory to decrease lock contention

## Resources

We will use [AFL++](https://github.com/AFLplusplus/AFLplusplus) as the baseline code for the parallel fuzzer. Our project is based on the paper ["Program State Sensitive Parallel Fuzzing for Real World Software"  (J. Ye _et al._)](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8668503); the paper provides a high-level algorithm and methodology of tackling the aforementioned challenges, and the performance targets that we would like to reach through our implementation. It does not provide code or implementation. We will initially use our own machines, and given enough time, test on other machines as well (such as GHC machines or PSC machines).

## Goals and Deliverables

Our primary goal of this project is to have a succesful implementation of PAFL described by the paper; by successful, we mean that we would like to have a working implementation that achieves a similar, if not better, performance improvements compared to the current method of running multiple AFL instances in parallel.

If there is enough time after a successful implementation, we hope to be able to compare the performances on different machines with different number of cores. Furthermore, we will explore further areas to improve on this implementation of parallel fuzzing.

If the progress is slower than expected, we hope to at least implement the first part of the two challenging aspects of improving performance of parallel fuzzing, provided in the paper - balancing workload by properly reallocating states to processors.

Because a fuzzing campaign of a program can take from a day to even months (especially if it is a complicated enough program to be utilizing multi-core system), we will not have an interactive demo; instead, we will produce a speedup graph compared to the baseline AFL implementation and the speedup that the paper argues to have. If we get to testing on different machines, we will compare the performances between different machines and number of cores as well.

## Platform Choice

Since we are using AFL++ as our baseline code, we will build upon it by using C and C++. The computer we will use initially throughout implementing PAFL is our own machines, which is enough to ensure correctness and test for performance improvements. Once a correct implementation is reached, we will further test on GHC machines and PSC machines.

## Schedule

### Week 1 (November 1 - November 7)

- ~~Work on shaping out the overall project, and write the proposal~~
- ~~Read and understand the paper we will be using, as well as other papers on fuzzing~~
- ~~Read through the documentation and code of AFL++ to understand their implementation of program states~~

### Week 2 (November 8 - November 14)

- ~~Read more through the documentation and code of AFL++~~
- ~~Try compiling and running AFL in vanilla parallel mode~~

### Week 3 (November 15 - November 21)

- ~~Start working on implementing PAFL~~
- ~~Write Milestone Report; due on November 22, 9 am~~

### Week 4 (November 22 - November 28) <= we are here

- Continue working on PAFL

### Week 5 (November 29 - December 5)

- Finish up implementation
- Start testing for performance

### Week 6 (December 6 - December 9)

- Finish up testing for performance
- Write Final Project Report; due on December 9, 11:59 pm
- Prepare for Project Poster Session

### Project Poster Session (December 10)

- Look at other people's projects
- Have fun

