# PH Evaluator

[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/HenryRLee/PokerHandEvaluator/CI?color=green&logo=github)](https://github.com/HenryRLee/PokerHandEvaluator/actions/workflows/ci.yml)

A Poker Hand Evaluator based on a Pefect Hash Algorithm

## Overview

Efficiently evaluating a poker hand has been an interesting but challenging
problem. Given two different poker hands, how to determine which one is
stronger? Or more generally, given one poker hand, can we assign a score to
it indicating its strength?

Cactus Kev once gave [an answer](http://suffe.cool/poker/evaluator.html) for
a five-card poker hand evaluation. With smart encoding, it ranks each hand
to 7462 distinct values.

Still, Kev's solution is specific for a five-card hand. To evaluate a
seven-card poker hand (which is more popular because of Texas Hold'em) using
Kev's algorithm, one brute force solution is to iterate all 7 choose 5
combination, running his five-card evaluation algorithm 21 times to find the
best answer, which is apparently too time-inefficient. Omaha poker would be
even more complicated, as it requires picking exactly two cards from four
player's cards, and exactly three cards from five community cards. Using
brute force, it would take 60 iterations (5 choose 3 multiplied by 4 choose 2)
of Kev's 5-card evaluation algorithm.

[PH Evaluator](https://github.com/HenryRLee/PokerHandEvaluator) is designed
for evaluating poker hands with more than 5 cards. Instead of traversing all
the combinations, it uses a perfect hash algorithm to get the hand strength
from a pre-computed hash table, which only costs very few CPU cycles and
considerably small memory (~100kb for the 7 card evaluation). With slight
modification, the same algorithm can be also applied to evaluating Omaha
poker hands.

## Algorithm

This [documentation](Documentation/Algorithm.md) has the description of the
underlying algorithm.

## C/C++ Implementation

The [cpp](cpp) subdirectory has the C/C++ implementation of the algorithm,
offering evaluation from 5-card hands to 7-card hands, as well as Omaha
poker hands.

One of the latest benchmark report generated by Google Benchmark:

```bash
2020-05-25 03:29:00
Running ./benchmark_phevaluator
Run on (2 X 2800.16 MHz CPU s)
CPU Caches:
  L1 Data 32 KiB (x1)
  L1 Instruction 32 KiB (x1)
  L2 Unified 1024 KiB (x1)
  L3 Unified 33792 KiB (x1)
Load Average: 0.84, 0.29, 0.11
-------------------------------------------------------------------
Benchmark                         Time             CPU   Iterations
-------------------------------------------------------------------
EvaluateAllFiveCards       42539892 ns     42539339 ns           16
EvaluateAllSixCards       358763068 ns    358754423 ns            2
EvaluateAllSevenCards    2712988225 ns   2712943774 ns            1
EvaluateRandomFiveCards        1924 ns         1924 ns       366811
EvaluateRandomSixCards         2031 ns         2031 ns       347350
EvaluateRandomSevenCards       2296 ns         2296 ns       306389
EvaluateRandomOmahaCards       3709 ns         3709 ns       189019
```

|   | Number of Hands | Time Used | Hands per Second | Memory Used |
|---|---|---|---|---|
| All 5-card Hands | 2598960 | 42539892 ns | 61 M/s | 404K |
| All 6-card Hands | 20358520 | 358763068 ns | 56 M/s | 404K |
| All 7-card Hands | 133784560 | 2712988225 ns | 49 M/s | 404K |
| Random 5-card Hands | 100 | 1924 ns | 51 M/s | 404K |
| Random 6-card Hands | 100 | 2031 ns | 49 M/s | 404K |
| Random 7-card Hands | 100 | 2296 ns | 43 M/s | 404K |
| Random Omaha Hands | 100 | 3709 ns | 26 M/s | 404K |

* I didn't measure the memory properly. Basically 404K is the maximum
memory used in all the evaluation methods.
* The performance on random samples are slightly worse due to the overhead
of accessing the pre-generated random samples in the memory.

## Python Implementation

The [python](python) subdirectory has the latest Python implementation.

Currently it supports 5-card, 6-card and 7-card poker hands evaluation, as well
as Omaha poker hands evaluation.

You can install the library using `pip`:

```
pip3 install phevaluator
```

You can find more examples from [here](https://github.com/HenryRLee/PokerHandEvaluator/tree/master/python#using-the-library).

Thanks to the community for contributing to the Python implementations. Especially
[azriel1rf](https://github.com/azriel1rf),
[ohwi](https://github.com/ohwi),
and [bensi94](https://github.com/bensi94).

## Other Implementations

[PHE](https://github.com/thlorenz/phe) is a Javascript port, developed by Thorsten Lorenz.

[41Poker](https://github.com/41semicolon/41poker) is another Javascript port, developed by 41semicolon.

[poker](https://pub.dev/packages/poker) is a Dart port, developed by Kohei.

[ghais/poker](https://github.com/ghais/poker/blob/main/src/Poker/Holdem/Evaluate.hs) contains a Haskell implementation of the evaluator.

[gophe](https://github.com/mattlangl/gophe) is a Go port, developed by mattlangl.

[poker-handle](https://github.com/pocketberserker/poker-handle/tree/main/src/poker) has a TypeScript port, developed by pocketberserker.

[PokerHandEvaluator.cs](https://github.com/travisstaloch/PokerHandEvaluator.cs) is a C# port, developed by travisstaloch.

[poker_engine](https://github.com/aleo101/poker_engine) is a Rust port, developed by Alexander Leones.

[Poker-Calculator](https://github.com/tryabin/Poker-Calculator) contains a CUDA implementation of this evaluator.

## Awesome Use Cases

### A simple Hold'em pre-flop equity estimator

A reddit user coded a Hold'em pre-flop equity estimator in C++ using the PHEvaluator library.

https://www.reddit.com/r/poker/comments/okk5qn/i_ran_1m_runouts_of_random_play_to_get_a_sense_of/

The source code can be found in [sim.cc](https://gist.github.com/bwasti/c2ca972c57f4fb581813f82f010c7cb2).

![pre-flop equity estimator](https://i.redd.it/ibav59awmab71.jpg)

## A Python example for Monte Carlo simulation

An article about Monte Carlo simulation of Texas Hold'em.

[Estimating the outcome of a Texas hold’em game using Monte Carlo simulation](https://petrosdemetrakopoulos.medium.com/estimating-the-outcome-of-a-texas-holdem-game-using-monte-carlo-simulation-1be35be29036)

It's source code is in https://github.com/petrosDemetrakopoulos/TexasHoldemMonteCarloSimulation
