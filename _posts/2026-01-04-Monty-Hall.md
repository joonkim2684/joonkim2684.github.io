---
layout: post
title: Proving the Monty Hall Problem, Intuitively
date: 2026-01-04
description: Here's a slick way to think about the Monty Hall problem, without all of the conditional probability mess.
tags: probability
categories: math
---

### The Monty Hall Problem

> You are in a quiz show, and the show host (let's call him Monty) presents you with three doors. Only one is a door to a million dollars! Here's the twist: once you choose a door, Monty opens one of the doors that you didn't choose, revealing it to be empty. Then, Monty offers you one last chance to change your choice of the unrevealed door. Do you take it?

This is one of the perennial problems that is used to educate students on the unituitive nature of probability. To an unsuspecting mind, the answer is quite trivial: it doesn't matter, each door still being a coin flip. Surprisingly, after calculating some gory conditional probabilities and sample spaces, we find out that switching actually has a $$2/3$$ chance of winning, whereas staying only wins $$1/3$$ of the time! How can this be?

### Switching Before Choosing?

The problem is designed in such a way that tricks our brains; we tend to believe that everything that's hidden from us has to be equally likely because, well, that's our best guess. However, things become a bit shaky when we view the problem in a different order:

> You are in a quiz show, and the show host (again Monty) presents you with three doors.  Only one is a door to a million dollars! Here's the (second) twist: before choosing a door, Monty asks whether you are going to switch later. Once you choose a door, Monty opens one of the doors that you didn't choose, revealing it to be empty. Then, you make the switch depending on what you answered before.

So nothing changes, except you simply promise Monty that you will switch even before you choose a door. What's so different about this game?

Actually, the whole point is that the two games **should** be the same, assuming that there exists a dominant strategy! See, if you can prove that either *always* switching or *always* staying are advantageous, then you should indeed *always* pick that choice. Anything else would be suboptimal. Moreover, Monty cannot change the rule of the game depending on your answer, which means that it does not matter that you reveal your strategy at any point of the game. Game theoretically speaking, this is equivalent to playing *best offense* when the opponent already fixed their strategy. Your opponent Monty, being the generous show host, already fixed their strategy: the Monty Hall problem itself. Now it's your turn to exploit the game as much as possible, and Monty can do nothing about it.

Hence, analyzing the new game is equivalent to analyzing the original game. And now we get to the interesting observation:

*"Always switching is effectively choosing which door to give up."*

If you think about it for a moment, this is really what's happening; one of the two unchosen door is revealed by Monty, and the other door is revealed by your switch. The only door that is never revealed is your original choice. But then, since Monty always opens an empty door, if you chose an empty door originally, then the remaining one has to hold a million dollars! On the other hand, if you chose the million dollar door originally, you would have no chance of winning regardless of Monty's choice. So the problem simply reduces down to the probability that you don't choose the million dollar door at the start, which is trivially $$1/3$$.

In some sense, it's like making a contract: by promising to Monty that you will give up your chance of winning entirely if you make the wrong choice (choosing the correct door then switching away from it), you ensure that when that doesn't happen, you always win (choosing an empty door then switching to the correct one). This is clearly advantageous to you. And the most important part is that, you can just make the contract by yourself! Monty doesn't have to listen to your contract to change anything; everything can be calculated in your head even before the original choice, then when Monty asks, you just follow your decision.

### So What?

The Monty Hall problem is so well studied by now, that almost no one who studied it for a bit is fooled. Nonetheless, I had trouble explaining in words why switching is truly advantageous, even though I knew it by mathematics. Then, somewhere down the line, I thought of this explanation. Now, I cannot be convinced otherwise; in fact, it took me some time to recall why this is a tricky problem! This problem is special to me because it was probably the first time I was able to convince myself that something is true with my own reasoning. And it's mostly these experiences that make me keep studying this subject.
