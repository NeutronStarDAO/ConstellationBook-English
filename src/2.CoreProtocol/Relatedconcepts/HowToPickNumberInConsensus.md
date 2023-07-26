# How to pick the number of consensus committee ?

First and foremost, we have a large group of individuals (let's call it U), consisting of both honest and dishonest people.

Now, we want to randomly select a small subset of individuals from this group (referred to as G) and calculate the probability of honest people within this subgroup. Ideally, we would like everyone selected to be honest. However, it is clear that we cannot guarantee that all chosen individuals will be honest, so let's strive to maximize the probability of selecting honest people!

<br>

So, how do we calculate this probability?

We employ the **hypergeometric cumulative distribution function** (CDFhg) and the **binomial cumulative distribution function** (CDFbinom) formulas to determine the smallest subgroup size that ensures the probability of honest individuals exceeds an acceptable threshold.

We use a mathematical formula called the **hypergeometric cumulative distribution function** (CDFhg). Put simply, this formula helps us calculate the probability of honest individuals based on the total number of people in group U, the number of honest people, and the size of the subgroup G we wish to draw.

However, we want to establish a minimal subgroup size (denoted as n) such that the probability of honest individuals surpasses an acceptable threshold (1 - ρ). This way, we can employ this subgroup size in practical applications.

If our group U becomes exceedingly large, we utilize another formula called the **binomial cumulative distribution function** (CDFbinom), which is based on the binomial distribution. This formula is similar to the previous one but is applicable to larger group U sizes.

Lastly, by comparing various thresholds (ρ) and the proportion of dishonest individuals (β), we identify the smallest subgroup sizes suitable for different scenarios. These sizes can be applied to practical protocols, ensuring that the probability of honest individuals in our drawn subgroup G is sufficiently high.

Now let's talk about CDFhg and CDFbinom.



## Hypergeometric cumulative distribution function (CDFhg)

In this formula, we use \\(CDFhg(x, n, M, N)\\) to represent the cumulative distribution function of the hypergeometric distribution.

Here:

- N: Total number of people in group U
- M: Number of honest people in group U
- n: Size of small group G formed by randomly selecting
- x:  The maximum number allowed of honest people in the small group G

Then, we can calculate the probability of honest people in the randomly selected small group G by the following formula:

$$ Prob[G honest] = CDFhg(⌈n/2⌉ − 1, n, ⌊|U|/β⌋, |U|) $$

This formula tells that: given the number of honest and dishonest people in a group U, we can calculate the probability of getting honest people when drawing a specific sized small group G.



## Binomial cumulative distribution function (CDFbinom)

When the size of group U approaches infinity, we can use the binomial distribution to approximate the hypergeometric distribution. The cumulative binomial distribution function is expressed as \\(CDFbinom(x, n, p)\\).

Here:

- p: The success (honest people) probability for each draw
- n: Size of small group G formed by randomly selecting
- x: The maximum number allowed of honest people in the small group G

We can calculate the probability of honest people in the randomly selected small group G by the following formula:

$$ Prob[G honest] ≥ CDFbinom(⌈n/2⌉ − 1, n, 1/β) $$

This formula tells that in a very large group , what are the probabilities of getting honest people when drawing a specific sized small group G.

<br>

Both of these mathematical formulas are used to calculate the probability of honest individuals in a randomly sampled subgroup G. The CDFhg is applicable to group U with a finite size, while the CDFbinom is suitable for infinitely large group U sizes. These formulas aid us in identifying appropriate subgroup sizes to ensure the probability of honest individuals surpasses an acceptable threshold.

