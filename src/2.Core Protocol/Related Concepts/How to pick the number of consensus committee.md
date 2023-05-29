# How to pick the number of consensus committee ?

Assume we have a very large group of people (called 'U' ), which contains honest people and dishonest people.

Now, we want to randomly select a small number of people (called 'G') from the group, and calculate the probability of selecting honest people in this small group. We hope that all people selected are honest. However it is impossible to guarantee that all selected people are honest,which is obvious.  What we could do is to make the probability of selecting honest people gets as large as possible!

How could we calculate such probability?

We could use the hypergeometric cumulative distribution function (CDFhg)  and binomial cumulative distribution function (CDFbinom) to determine the minimum group size, ensuring that the probability of selecting honest people is higher than an acceptable threshold.

The hypergeometric cumulative distribution function (CDFhg) helps us compute the probability of honest people, according to the total number of people in 'U', the number of honest people and the size of small group G  we randomly select.

We then want to find the minimum sample size 'n' for the small group G , such that the probability of having honest people in G exceeds our acceptable threshold (1 - ρ). By determining this minimum n, we can then use this group size in practice to ensure the probability of sampling honest people meets our requirement.

What if we have a group U that is significantly large? We could use the binomial cumulative distribution function (CDFbinom), which works similarly to the previous one but suitable for larger size of U.

Lastly, by comparing different thresholds (ρ) and the proportions of dishonest people (β), we could find the minimum small group sizes suitable for different scenarios. These sizes can be applied to actual protocols to ensure that the probability of honest people in the small group G we sample is high enough.

Now let's talk about CDFhg and CDFbinom.



## Hypergeometric cumulative distribution function (CDFhg)

In this formula, we use CDFhg(x, n, M, N) to represent the cumulative distribution function of the hypergeometric distribution.

Here:

- N: Total number of people in group U
- M: Number of honest people in group U
- n: Size of small group G formed by randomly selecting
- x:  The maximum number allowed of honest people in the small group G

Then, we can calculate the probability of honest people in the randomly selected small group G by the following formula:
$$
Prob[G honest] = CDFhg(⌈n/2⌉ − 1, n, ⌊|U|/β⌋, |U|)
$$
This formula tells that: given the number of honest and dishonest people in a group U, we can calculate the probability of getting honest people when drawing a specific sized small group G.



## Binomial cumulative distribution function (CDFbinom)

When the size of group U approaches infinity, we can use the binomial distribution to approximate the hypergeometric distribution. The cumulative binomial distribution function is expressed as CDFbinom(x, n, p).

Here:

- p: The success (honest people) probability for each draw
- n: Size of small group G formed by randomly selecting
- x: The maximum number allowed of honest people in the small group G

We can calculate the probability of honest people in the randomly selected small group G by the following formula:
$$
Prob[G honest] ≥ CDFbinom(⌈n/2⌉ − 1, n, 1/β)
$$
This formula tells that in a very large group , what are the probabilities of getting honest people when drawing a specific sized small group G.

These two formulas are both used to calculate the probability of honest people in a random sample group G. CDFhg is for a finite sized group U, while CDFbinom is suitable for an infinite large froup U. These formulas help us find an appropriate group size to ensure the probability of honest people is higher than an acceptable threshold we can accept.