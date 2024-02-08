Secret Sharing is an important technique in cryptography that provides a method for distributing secret information among multiple entities, thereby achieving control over the secret.

> It can implement both the \(t\) of \(n\) scheme and reduce the size of signature results, falling into the category of threshold signatures.
>
> The key is divided into \(n\) pieces of key fragments, each saved by one of the \(n\) members. As long as at least \(t\) fragments of the secret are gathered, the complete key can be recovered, thus completing the signature.

<br>

## What is Secret Sharing

In simple terms, Secret Sharing involves splitting a secret into multiple "shares" and distributing them to different entities. Only when a sufficient number of "shares" are collected can the secret be reconstructed. For example, a password can be split into 5 shares and given to individuals A, B, C, D, and E. It is specified that only by simultaneously collecting at least 3 shares held by different people can the password be reconstructed. Therefore, it is impossible to derive the original password using the shares of 1 or 2 individuals. This prevents information leakage from a single entity.

<div class="center-image">
<img src="assets/秘密共享/image-20231226145124652.png" style="zoom:39%;" />
</div>

Secret Sharing can:

* Enhance the security of secrets by preventing single-point leakage through splitting and distribution.
* Implement access control for secrets by specifying the minimum number of shares required to reconstruct the secret, achieving access policy control.
* Improve the availability of secrets, allowing reconstruction of secret shares even if some shares are lost, resetting key fragments.

<br>

## Application Scenarios

Secret Sharing technology is widely used in various scenarios, such as:

* Key management: Encrypt certificates or keys and distribute them into multiple shares to multiple certificate authorities or key management nodes, avoiding single-point failures.
* Multi-signature accounts in blockchain: Require transactions to be approved by a certain number of nodes before taking effect.
* Sensitive data storage: Split critical data into multiple shares and store them with different cloud service providers to prevent data leakage from a single provider.
* Voting systems: Split voting keys based on threshold policies, and only collecting enough voting nodes can open the vote count.
* Military permission control: Different levels of command need to collect the corresponding number of key shares to activate weapon systems.

In practical applications, different Secret Sharing algorithms can be chosen based on the needs, determining the number of distributed shares, and the minimum number of shares required for reconstruction, thus achieving a customized access structure.

<br>

## Secret Sharing Algorithms

To implement Secret Sharing, relevant mathematical algorithms are required. The initial Secret Sharing algorithms were independently proposed by G. R. Blakley and A. Shamir in 1979. Common Secret Sharing algorithms include:

* Shamir's Secret Sharing: Based on polynomial interpolation, it is the most commonly used algorithm.
* Asmuth-Bloom Secret Sharing: Shared using the Chinese Remainder Theorem.
* Blakley's Geometric Secret Sharing: Based on the intersection points of hyperplanes.

These algorithms usually rely on mathematical principles (such as polynomials, vector spaces, etc.) to split the secret into multiple shares, and only by collecting a sufficient number of shares can the secret be reconstructed. Due to space constraints, the details of the latter two algorithms are not discussed here, with a focus on introducing Shamir's Secret Sharing.

<br>

## Shamir's Secret Sharing

Shamir's Secret Sharing is a secret sharing algorithm based on polynomial interpolation, proposed by Adi Shamir in 1979.

It mainly involves two processes: splitting the secret and reconstructing the secret.

<br>

### Splitting the Secret

Choose a prime number \(p\), and construct a random polynomial of degree \(t-1\) in the integer ring \(Z_p\):

$$
f(x) = a_0 + a_1x + a_2x^2 + \ldots + a_{t-1}x^{t-1} \mod (p)
$$

Where \(p\) is a large prime, and \(f(0) = a_0 = s\) (where \(s\) is the secret), and \(s < p\).

Generate \(t-1\) random numbers \(a_1, a_2, \ldots, a_{t-1}\) less than \(p\), and randomly select \(n\) distinct integers \(x_1, x_2, \ldots, x_n\).

Evaluate the polynomial at \(n\) points to obtain \(n\) values \(s_1 = f(x_1), s_2 = f(x_2), \ldots, s_n = f(x_n)\).

Distribute the \(n\) calculated values to \(n\) participants, where the \(i\)-th participant receives \((x_i, s_i)\) (as the secret that the participant needs to strictly keep).

Finally, destroy \(f(x)\). According to the properties of the polynomial function, less than \(t\) participants cannot reconstruct this polynomial.

<br>

For example, let's take the example of \(t = 3, n = 4\). Here, \(t\) is the reconstruction threshold, and \(n\) is the total number of secret shares.

Assuming the secret \(s = 2\), \(p = 23\), the constructed \(f(x)\) is:

$$
f(x) = 2 + 3x + 2x^2 \mod (23)
$$

Choosing \(x_1 = 1, x_2 = 2, x_3 = 3, x_4 = 4\), and evaluating the function, we get \(f(1) = 7, f(2) = 16, f(3) = 6, f(4) = 0\). Thus, 4 secrets are split.

<br>

### Reconstructing the Secret

Since we have a \(t, n\) secret sharing (\(3, 4\)), with 4 secrets split, knowing any 3 of them can reconstruct the initial secret.

Here, \(t\) is set to \(3\), meaning the threshold for reconstructing the secret is \(t\).

Randomly select 3 sets of data \((1, 7), (3, 6), (4, 0)\) and use Lagrange interpolation formula for reconstruction.

<br>

In the Shamir Secret Sharing algorithm, the secret \(s\) is the constant term of the polynomial. Given three points, we can use Lagrange interpolation to find the corresponding polynomial.

The general form of the Lagrange interpolation polynomial is:
$$
L(x) = \sum_{i=1}^{k} y_i \prod_{j=1, j\neq i}^{k} \frac{x - x_j}{x_i - x_j} \mod p
$$
where \((x_i, y_i)\) are the given points.

$$
L(x) = 7 \cdot \frac{(x-3)(x-4)}{(1-3)(1-4)} + 6 \cdot \frac{(x-1)(x-4)}{(3-1)(3-4)} + 0 \cdot \frac{(x-1)(x-3)}{(4-1)(4-3)} \mod 23
$$
Simplifying, we get:

$$
f(x) = 2 + 3x + 2x^2 \mod (23)
$$
This matches the original polynomial we started with. Therefore, the secret \(s\) is the constant term of the polynomial, i.e., \(s = 2\). So, the secret \(s\) is 2.

<br>

This is the core idea of Shamir's Secret Sharing algorithm, which applies knowledge of abstract algebra and polynomial interpolation theory to provide information-theoretic security guarantees. This secret can be a private key or extended to any other information, such as encrypted information, puzzle answers, secret wills, etc.

It not only enables multi-party management but also provides a certain fault tolerance mechanism, allowing up to \(n - t\) share data to be lost.

<br>

However, technically, it belongs to single signature since, in the end, a private key signature needs to be reconstructed, rather than directly splitting multiple private key fragments for signing.

Threshold signatures, derived later, allow signing with the split private key fragments. Each member generates a signature fragment using their private key fragment, and by aggregating enough signature fragments, the complete signature can be reconstructed.

In the entire process of threshold signatures, the complete private key is never exposed, ensuring high security. Each member only knows their own portion of the private key fragment.

<br>

### Shortcomings

But!

The original Shamir key sharing scheme can only split the key and has several issues.

Firstly, the key distributor who knows the complete private key has single-point control over the key, posing a possibility of malicious behavior, such as issuing incorrect private key fragments to some members.

Additionally, holders of private key fragments may provide false fragments or distribute private keys only to a subset of people, not meeting the threshold! For example, if there are 7 members with a threshold of 5, but private keys are only given to 4 members - it cannot be used! Therefore, the integrity of distributing private keys also needs to be verified.

<br>

To address these issues, an improved mechanism based on Shamir key sharing is Verifiable Secret Sharing (VSS).

Regarding VSS, we will directly discuss the practical Feldman VSS scheme. Although there are other scheme designs from Shamir's key sharing (1979) to Feldman's VSS scheme (1987).

> The concept of VSS was first proposed by Benny Chor, Shafi Goldwasser, Silvio Micali, and others in 1985.

<br>

## Feldman Scheme - Verifiable Secret Sharing

The Feldman Scheme is a verifiable secret sharing technique that allows the owner Alice of a key to split the key into multiple shares distributed to others. Moreover, these individuals can verify whether the received key share is correct, but they cannot obtain the entire original key.

To make the data of the distributed secret fragments verifiable, the person distributing the private key fragments, besides providing the private key fragments, must also provide the corresponding commitment \((c_0, c_1,... )\).

<br>

The basic idea of this scheme is as follows:

Alice, as the private key distributor, selects a large prime number \(p\) and a generator \(g\), where \(g\) belongs to \(Z_{p}^{*}\) and is a \(q\)-order element. In practice, the key sharing involves operations in the cyclic group of a finite field, using the public \(g\) as the generator.

\(q\) is a large prime factor of \(p - 1\). Publicly disclose \(p, q, g\).

\(s\) is a randomly generated original key, \(t\) is the threshold, and \(n\) is the total number of members.

Generate the polynomial:
$$
f(x) = a_{0} + a_{1}x + a_{2}x^{2} + \ ...\ +a_{t-1}x^{t-1} \mod(p)
$$
where \(a_0\) represents the private key, i.e., the secret of the polynomial.

Compute the commitment:
$$
c_0=g^{a_0}, \ c_1=g^{a_1}, \ c_2=g^{a_2}, \ ..., \ c_{t-1}=g^{a_{t-1}}
$$

Alice publicly reveals the commitment \((c_0, c_1, \ ,\ c_2...\ ,\ c_{t-1})\) to all members.

Next, Alice needs to split the private key \(s\) into \(n\) shares \(s_i\) and distribute them to \(n\) members.

A member who receives \(s_i\) can use the commitment to verify the private key fragment: calculate \( g^{s_{i}} = {\textstyle \prod_{j=0}^{k-1}} (C_j)^{i_{j}} \mod{p} \).

If the result is equal, it indicates that this private key fragment is correct. Since the commitment is bound to the coefficients, if Alice provides a commitment not using the real coefficients of the polynomial equation, the verification will fail.

Everyone can verify their own private key fragments, but no one can obtain \(s\) unless all \(n\) people collaborate.

<br>

Thus, the Feldman Scheme achieves verifiable secret sharing, ensuring the security of the key while allowing each person to verify that they have received the correct key fragment. This has applications in many cryptographic systems and blockchain technologies.

<br>

## Updating Secrets - Dynamic Secret Sharing

The Feldman Scheme added a verification step based on the original Shamir Scheme, addressing the issue of dishonest secret distributors in traditional secret sharing.

However, this scheme assumes that attackers cannot obtain enough private key fragments throughout the entire system's lifecycle. A more practical scenario is that attackers can slowly infiltrate different members over different time periods, compromising them one by one.

In cases where the secret itself has a long lifecycle, it is vulnerable to being compromised one by one. For example, if a node is subject to a virus attack, or if private key fragments are leaked or forgotten, verifiable secret sharing schemes may not maintain optimal security against prolonged destructive attacks.

While it is possible to mitigate this problem by changing the original private key (secret), there are situations where the private key must remain unchanged for an extended period (such as in business or military secrets).

<br>

Dynamic secret sharing schemes address the security issues of secret sharing schemes in long-term storage without changing the secret.

By periodically replacing private key fragments, each time a new set of fragments is replaced, any private keys obtained by attackers in the previous cycle become invalid.

This allows adjusting the length of the private key fragment retention period based on the potential threat to the key. For example, if the secrecy level of the private key is very high, the cycle for replacing private key fragments should be short and frequent. Conversely, for lower-security levels, the replacement cycle can be longer.

This ensures the security of the private key within each cycle, and expired private key fragments do not affect the newest ones. It eliminates the cumulative effect of secrets obtained by attackers in the previous cycle as time progresses.

In this way, even if an attacker gains a previous set of private keys for a node, they cannot forge the identity of that node in the next cycle. This is because the node has updated its private key. The attacker is unable to obtain the new private key for signing.

It's important to note that if an attacker not only obtains the old private key but also maintains control of the node in the next cycle, they can create a new key pair and broadcast it. However, since the real node will also broadcast its true new public key, there will be conflicting public keys in the network. Other nodes will detect this and determine that the node has been compromised. At this point, the node needs to be reset, and a completely new operating system and private key need to be installed.

<br>

There have been several dynamic secret sharing schemes proposed, and the scheme proposed by [Amir Herzberg](https://sites.google.com/site/amirherzberg/home) in 1995 is a classic one. This scheme dynamically adapts Shamir's secret sharing scheme.

<br>