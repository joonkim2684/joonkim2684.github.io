---
layout: post
title: Two Interesting Randomized Algorithms (1)
date: 2025-09-27 
description: I present two very interesting algorithms I learned in CS174, based only on elementary probability theory. Part 1.
tags: algorithms, probability
categories: algorithms
---

### CS174 and What it Taught Me

I am writing this post to appreciate once more the absolutely fascinating contents of CS174: Combinatorics and Discrete Probability, taught by the quite legendary Professor [Alistair Sinclair](https://people.eecs.berkeley.edu/~sinclair/). I have to mention that the course description itself is quite misleading (and scares away most CS students) because the actual content is rooted in *randomized algorithms*, which is a class of algorithms that allows the use of a perfectly random bit string for decision making. Interestingly, just by allowing the computer to sometimes make random choices, we can obtain extremely simple and elegant solutions to seemingly hard problems (with some caveats, of course). Other times, we require the output to be randomized, which requires the algorithm to be randomized in the first place. And inevitably, probability comes in for the analysis of runtime and correctness.

What was truly inspiring for me was the fact that we can get so far in analysis without even invoking the concept of variance or other advanced topics in probability. Simple distributions and expectation arguments can be powerful, if used correctly. The two algorithms I am about to present showcase just that, albeit they challenge you to understand randomness at a deeper level. This is part 1 of this series, covering one of the two that I prepared. I left some very simple exercises for the reader to fill in the details because that's what math textbooks do. Just kidding, to make the reading a bit more interactive and for sanity checks.

### Simulating a Biased Coin with an Unbiased Coin

Given an unbiased (fair) coin with heads probability $$1/2$$, how can you simulate any biased coin with head probability $$p$$?

#### A Naive First Try

Let's do a quick example with $$p=1/4$$, that is, we want to return heads only 1 out of 4 times. It is not difficult to observe that we only have to flip twice and report heads for the sequence **HH** and tails for all others. Another example, $$p=5/8$$. This is a bit more complicated, but in fact we only need three flips [Exercise: why? Hint: Consider predefining what to be heads]. The trend goes on, and it seems that we can simulate any $$p=n/2^m$$ with only $$m$$ flips! Is this it? Did we just find a good algorithm? To convince you that we're not done yet, I'd like to take a small digression to a different question: the converse.

#### Digression: The Converse

Perhaps you are familiar with the converse question: how to simulate an unbiased coin with a biased one with head probability $$p$$? The answer is quite simple: flip the coin twice repeatedly until you get differing sides and report the first one. The analysis is that the sequence **HT** and **TH** must have the same probability $$p(1-p)$$ of showing up, so we can take the first one to be **H** and the second **T** WLOG. 

What this answer lacks is the algorithmic analysis: it's surely correct, but how *efficient* is it - in other words, how *fast* is it? Consider the case of a heavily biased coin, say $$p = 0.99$$. This is quite bad, since our probability of hitting either stopping condition is extremely low: $$2*0.99*0.01 = 0.0198$$. Then, by the expectation of a geometric, we would need to run $$1/0.0198 \approx 50.5$$ iterations of two flips, so 101 flips in expectation. In fact, this algorithm is guaranteed to perform worse as $$p$$ gets very large or very small [Exercise: why both?]. So in some sense, it counteracts its purpose of correcting (potentially very heavily) biased coins by design by taking longer the more biased the coin is!

#### The "Real" Problem

The moral of the story for the converse problem is that we probably do not want an algorithm that performs poorly for some edge cases. Turning back to our original proposal, we now face a new challenge: can we say that our algorithm truly runs efficiently for all possible $$p$$ values? I'll give you a good one - $$p = 5/2^{10000000000}$$. This is a valid $$p$$ since it is between [0, 1]. However, we need 10e10 flips! Even worse: $$p=e/3$$. Here, we can't even define its denominator as a power of 2. More generally, since our algorithm needs to know in advance exactly what the $$log_2$$ of the denominator will be, it fails miserably when the denominator is simply not a power of two.

If you know some concepts in countability or real analysis, this observation should immediately trigger a fire alarm in your brain. Indeed, our algorithm succeeds for countably many values of $$p$$, but it fails for *uncountably* many of them as well. Putting it another way: for all possible values of $$p$$, the successful values are infinitely smaller than the unsuccessful ones. This is extremely concerning, since we want our algorithm to be generalizable to all values of $$p$$, but we realized that we have some mines in our backyard, and in fact, they practically cover the entirety of the backyard! And even without fancy knowledge, you should have figured out by now that our algorithm needs some refinement.

#### Being Lazy is Being Smart

It seems that the problem is that we are trying for infinite precision. Really, do we have to flip 10e10 flips just to say that it's tails the vast majority of the time? Can we do something a bit smarter?

Turns out this is the key intuition for solving the mess I described above. First, we need to change our perspective. We know that every number, even real numbers, can be represented as binary numbers [Exercise: Construct an explicit converter between decimal and binary numbers, or just convince yourself of this claim]. And since $$p \in [0, 1]$$, it will always be in the form of $$p = 0.b_1b_2b_3...$$ where each $$b_i$$ is a binary digit [Exercise: Why?]. What this expression reveals is that even though the binary strings may be extremely long or even infinite, we only need to look at the first few. Think of it this way: we know that flipping $$n$$ times gives us a uniform sampler for $$2^n$$ elements. If we treat the flips as binary strings as well, as $$0.f_1f_2f_3...$$ where $$f_i$$ is 0 if the i-th flip is heads and 1 if tails. Then, if we have $$b_1=0$$ and $$f_1 = 1$$, we already know that we are "unluckier than" the threshold needed to push us into returning tails, since $$p<1/2$$. Turning back to predefining a "Heads" set, if we line up all possible strings in lexicographical order, and take the first $$p$$ portion of them to be in the "Heads" set, we already know that even if we sample infinitely many flips from now on, we are assured to never land on the "Heads" set . However, if the two are the same, then we can conclude nothing. So we can simply do the same procedure until the digits differ [Exercise: Apply the same logic to $$b_0=1$$ and convince yourself that this works].

#### The Algorithm and Analysis

Here, I give the formal algorithm in pseudocode.

Input: $$p$$

Output: H/T according to a simulated biased flip of heads probability $$p$$

```
do

    b <= 1 if {(p * 2) >= 1} else 0
    p <= decimal(p * 2)

    f <= flip_fair_coin()

    if {b != f}
        out <= T if {f == 1} else H
        return out

while
```
In this implementation, b extracts the binary digit that immediately follows the decimal point, and p is updated such that the next most significant digit is elevated to its place. Then, a random fair coin flip is compared with it to terminate the algorithm upon differing. The correctness of the algorithm was somewhat justified above, and a more advanced, intuitive argument is given in the following section. Feel free to skip it, but at least there should be some hunch that this actually works as intended by now [Challenge Exercise: Rigorously prove the correctness]. 

The truly surprising part is the analysis of its runtime, and it is quite terse. The expected runtime of this algorithm is the time it takes for a bit to differ from some other predefined bit b. This is simply the expectation of a geometric of parameter 1/2, which is 2! What this means is that running a bunch of these biased coin simulations would only incur double the amout of flips that is needed to sample the same number of fair coins. In the asymptotics language, this reduces down to $$O(2) = O(1)$$, which is just saying that the runtime of the algorithm is completely independent of the input. This was what we wanted! The algorithm works for any real number that is a valid probability, since nowhere in the pseudocode does it require $$p$$ to be anything more specific.

#### A Posteriori Intuition: Binary Search [Advanced!]

Sampling a biased coin is the same as selecting a random point on a continuous interval [0, 1] and testing if that number is less than $$p$$. So we can make an analogy to binary search, where you move down the decision tree for every new flip. The tree that we are concerned with has infinite depth and infinitely many "leaf nodes" that biject with each real number on the real number line. (The term "leaf node" is a bit handwavy since the tree is infinite, but for the sake of the intuition, we can imagine that they "exist.") The value $$p$$ is also associated with a "leaf node," and there is a unique path from the root to it [Exercise: Why unique? Hint: Property of trees]. Then, a heads returned from a biased coin flip can be thought of as landing somewhere left of the $$p$$ node. Since every flip has equal probability at every stage, it will eventually sample a uniformly random "leaf node." Now, at any point of the traversal, if the path diverges from $$p$$'s path, we can ascertain that we fall into either the left or right side of $$p$$. So we are done. Return H/T according to your findings.

#### Conclusion

If you think about it, this is probably how real computers give you random numbers, since computers can easily sample "fair" flips. The upshot of this is that we are now free to use any biased coin flips in the analysis of other randomized algorithms without worrying about the runtime of that sampling, which can simplify things a lot! Finally, the thought process behind the algorithm illustrates how an alternative representation of the same quantity can open up new ideas for design.
