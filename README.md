
[Final Report Link](418_final_report.pdf)

[Milestone Report Link](milestone___report.pdf)

[Proposal Link](PROPOSAL.md)

## Summary

Fuzzing is an automated software testing technique that involves providing different types of inputs to a given program and monitoring the behavior of the program. The most prevalently used repository for fuzzing is AFL++, a fork of the original AFL (American Fuzzy Lop) developed by Michał Zalewski. In fuzzing, there are two main factors that determine how effective the fuzzing session is, namely the number of program paths exposed in a given timeframe and the number of unique bugs exposed. In order to leverage parallelism to perform better in both directions, several research groups from around the world have devised different methods of parallel fuzzing. In this project, we explore the effectiveness of the seed-sharing version of AFL++, with regard to 1/2/4/6 threads and conclude that parallelism improves both metrics and also leads to more stable sessions. We also talk briefly about the other types of implementations that are of interest.

## Background

Fuzzing is a testing method where a randomly mutated program input is generated as test cases to find abnormal behaviors, i.e. program crashes. A purely randomized input is necessarily inefficient, as most programs require an input of a specific structure and have checks for them; as such, various fuzzing techniques exist. One such technique is coverage-based fuzz testing, which collects coverage information and uses it to guide seed selection and mutation.

American Fuzzy Lop (AFL) is a coverage-based fuzzer; it captures basic block transitions and their hitcounts into a bitmap with a lightweight, compile-time instrumentation. Then, it uses a genetic algorithm to mutate the seeds; a test cases that triggers new paths in the program is marked as interesting and is used as a seed for further expolration; other less interesting seeds are discarded to reduce unnecessary testing.

Algorithm of AFL's fuzzing code is shown below:

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

AFL has a parallel mode that makes it possible to run multiple instances of AFL on a multi-core system. Throughout the test, the instances will run independently, synchronizing any interesting seeds that were discovered and coverage information at certain intervals. More specifically, there is a main instance and several secondary instances running independently; the secondary instances periodically synchronize their own bitmaps and input queues with the main node, while the main node scans all secondary instances to update its data structure.

## Testing Method

**Hardware & Software Specifications:**

Ryzen 3600x CPU - 6 cores, 12 threads

VMware running on Windows 10 settings:
- Ubuntu 20.04.03 LTS
- 4GB RAM
- CPU 6 cores, 2 threads each (gave all cores to the VM)

**Test Parameters:**

- Test Program : Readelf, compiler environment variable “CC=afl-gcc ./configure”, binutils-2.25
- User Input      : /bin/ps
- Structure        : One main, Multiple secondary

**Test Method:**

Threads tested - 1, 2, 4, 6 threads. For each thread count, we tested 4 instances that reached 8 hours and averaged the statistics.

The following statistics were compiled:

- Paths found at 2 hour increments
- Maximum Gap between Number of Paths Found
- Bugs (crashes, hangs, etc.) found

## Goals

Our original goal of the project was to have a succesful implementation of PAFL described by [this paper](https://ieeexplore.ieee.org/document/8668503); however, it turned out to be that the AFL++ codebase was too difficult to modify as someone who had not been working with it previously, and would likely require more time and expertise to be able to implement PAFL. We also did not want to totally start from scratch and looked for ways to continue with AFL's parallelism within the remaining time; therefore, we decided to benchmark the current AFL++'s parallel mode, which leverages seed-sharing within a sync directory. The original proposal is linked [here](PROPOSAL.md).

Because a fuzzing campaign of a program can take from a day to even months (especially if it is a complicated enough program to be utilizing multi-core system), we will not have an interactive demo; instead, we will produce a graph comparing the various metrics of a fuzzing campaign, namely the paths discovered, the variance in the paths discovered, and the number of unique hangs found. If we get to testing on different machines, we will compare the performances between different machines and number of cores as well.

## Resources

**Source Code & General Knowledge:**

[Fuzzing basics](https://www.evilsocket.net/2015/04/30/Fuzzing-with-AFL-Fuzz-a-Practical-Example-AFL-vs-binutils/)

[AFL](https://github.com/google/AFL)

[AFL++](https://github.com/AFLplusplus/AFLplusplus)

**Other Implementations**

[Scaling AFL to a 256 thread machine](https://gamozolabs.github.io/fuzzing/2018/09/16/scaling_afl.html)

[Program State Sensitive Parallel Fuzzing for Real World Software](https://ieeexplore.ieee.org/document/8668503)

[EnFuzz: Ensemble Fuzzing with Seed Synchronization among Diverse Fuzzers](https://www.semanticscholar.org/paper/EnFuzz%3A-Ensemble-Fuzzing-with-Seed-Synchronization-Chen-Jiang/8754951ba8bbb42ff10e100fa853a5ce86af5ab1)

[Facilitating Parallel Fuzzing with Mutually-exclusive Task Distribution”](https://arxiv.org/pdf/2109.08635.pdf)

[UniFuzz: Optimizing Distributed Fuzzing via Dynamic Centralized Task Scheduling](https://arxiv.org/pdf/2009.06124.pdf)

**On Fuzzing**

[Fuzzing: On the Exponential Cost of Vulnerability Discovery](https://doi.org/10.1145/3368089.3409729)

[UNIFUZZ: A Holistic and Pragmatic Metrics-Driven Platform for Evaluating Fuzzers](https://arxiv.org/abs/2010.01785)

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

### Week 4 (November 22 - November 28)

- ~~Change directionality of project; read more papers on parallelization and performance evaluation~~
- ~~Work on actually making AFL run~~

### Week 5 (November 29 - December 5)

- ~~Start performance measurement~~

### Week 6 (December 6 - December 9)

- ~~Finish up measuring performance~~
- ~~Write Final Project Report; due on December 9, 11:59 pm~~
- ~~Prepare for Project Poster Session~~
