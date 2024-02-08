## Bilinear Mapping

Bilinear mapping is a very important cryptographic primitive, usually denoted as \\(e\\): \\(G_1\\) \\(\\times\\) \\(G_2\\) \\(\\rightarrow\\) \\(G_T\\). Where \\(G_1\\), \\(G_2\\) and \\(G_T\\) are cyclic groups of the same order.

> Definition: A bilinear mapping is a function from two vector spaces to a third vector space, such that the function is linear in each of its arguments. 

The bilinear mapping \\(e\\) needs to satisfy the following two conditions:

1. Bilinear For any \\(g_1\\in G_1\\), \\(g_2\\in G_2\\) and \\(a,b\\in\mathbb{Z}\\), we have:

$$e(g_1^a, g_2^b) = e(g_1, g_2)^{ab}$$

2. Non-degenerate There exist \\(g_1\\in G_1\\) and \\(g_2\\in G_2\\) such that: 

$$e(g_1,g_2)≠1_{G_T}$$

\\(e\\) needs to be homomorphic in each variable and cannot map all elements to 1.

If A, B, C are three vector spaces, and \\(e:A\\times B\\rightarrow C\\) is a bilinear mapping, then fixing A and varying B, the mapping from B to C is linear. Fixing B and varying A, the mapping from A to C is also linear. That is, keeping either parameter of the bilinear mapping fixed, the mapping of the other parameter to C is linear. 

So a bilinear function has two inputs, and is linear in each input separately.

For example, matrix multiplication and Cartesian product of two database tables are examples of bilinear pairing.

Matrix multiplication satisfies:

\\((A+B)C = AC + BC\\) (linear in C) 

\\(A(B+C) = AB + AC\\) (linear in A)

Cartesian product of two tables satisfies similar properties.

Bilinear mapping is a very powerful tool that enables many public key cryptosystems based on hard problems. For example, BLS signatures are built on the hardness of bilinear Diffie-Hellman problem.

Bilinear mappings are often implemented by pairing functions. Commonly used pairing functions include Tate pairing, Weil pairing, etc. These pairing functions can be constructed from specific elliptic curves. 

Bilinear mapping allows easy conversion between groups and utilizing relationships between groups to design various cryptosystems. It is a critical foundation for many cryptographic protocols and an important development in modern cryptography.

<br>

## BLS Signatures

BLS signature is a digital signature scheme based on bilinear pairings. 

The cryptography behind it is:

Choose a bilinear group \\((G_1, G_2)\\) where \\(G_T\\), \\(G_1\\) and \\(G_2\\) are cyclic groups of order a large prime \\(q\\). \\(P\\) is a generator of \\(G_1\\).

KeyGen: Generate keys by choosing a random private key \\(sk\\) and computing public key \\(pk=sk∗P\\). 

Sign: To sign a message \\(m\\), first hash it to get \\(H(m)\\). Then compute signature \\(σ = sk ∗ H(m)\\).

Verify: To verify a signature \\(σ\\), check if \\(e(P, σ) = e(pk, H(m))\\). Here \\(e\\) is a bilinear mapping. If equality holds, the signature is valid.

Why does this verification work? This relies on the **pairing function** we glossed over before, briefly introduced below:

There is a (or some) special function called \\(e\\) which takes as input two points \\(P\\) and \\(Q\\) on one (or two different) curves, and outputs a number: 

$$e(P, Q) → n$$

This function is considered special because it has some special properties. For example for a number \\(x\\) and two points \\(P\\) and \\(Q\\), no matter which point is multiplied, the function result is the same:

$$e(x∗P, Q) = e(P, x∗Q)$$

Furthermore: 

$$e(a∗P, b∗Q) = e(P, ab∗Q) = e(ab∗P, Q) = e(P, Q)^{ab}$$

Of course there are other properties, but the above are most relevant for signature verification.

The security of BLS signatures relies on the hardness of the bilinear Diffie-Hellman problem. It only requires two group operations for signing and verification, so is very efficient. It also has short signatures and provides randomness.

BLS supports simple signature aggregation: multiple BLS signatures can be aggregated into a single short signature through group operations. Verification is done by pairing the aggregated signature. 

Now given a single signature, let's look at aggregated signatures.

<br>

## Threshold BLS Signatures

In blockchain systems, signature verification is a very important and frequent operation to ensure security. Typically each transaction input requires a signature for verification. But for collaborative scenarios like multi-sig addresses, this can lead to rapidly growing transaction sizes and reduced processing efficiency.

To address this, researchers proposed the threshold BLS signature scheme. BLS signatures are a very efficient digital signature method, based on bilinear pairings, requiring just short signatures for security. The threshold BLS signature scheme builds on this, to aggregate multiple BLS signatures into one, greatly reducing storage and transmission costs.

<br>

How do threshold BLS signatures work? Let's briefly review the principles of BLS signatures themselves. It requires: a bilinear pairing function, two cyclic groups G1 and G0, a generator P of G1, and a hash function H. Key generation randomly chooses a number sk, and computes public key pk=sk\*P. Signing is hashing the message H(m), then computing signature σ = sk \* H(m). Verification checks if the pairing equation e(P, σ) = e(pk, H(m)) holds.

<br>

This verification equation also explains why signature aggregation is possible. If there are n signatures all signing the same message m, multiplying all the signatures together, the verification equation still holds. So the verifier only needs one aggregated short signature to verify n signatures simultaneously, greatly improving efficiency. 

Multiple BLS signatures can be simply multiplied to get the aggregated signature. That is, for \\((pk_1,m_1,σ_1),..., (pk_n,m_n,σ_n)\\), compute:

$$σ = σ_1 * ... * σ_n$$

To verify the aggregated signature σ, check the signature verification equation for each signature separately.

When the signed messages m are the same, verification can be further simplified to: 

$$e(g_1, σ) = e(pk_1 * ... * pk_n, H(m))$$

This requires only 2 pairing operations. This is the advantage of BLS signature aggregation.

<br>

However, direct signature aggregation is insecure, due to "rogue key attack" risks. To address this, threshold BLS signatures introduce a new hash function H1. This hash function takes all participating signers' public keys as input, and outputs a "weight" for each user. To compute the secure aggregated signature, each user's signature is raised to the power of their weight. The key idea is to introduce a random exponent for each public key, determined by the hash of all public keys.

Verification is similar, recomputing the weights for each user, aggregating public keys by the weights, and checking against standard BLS signature verification. So verifying multiple signatures has the same efficiency as a single signature. This mechanism effectively prevents rogue key attacks, as each user's signature contribution is precisely controlled.

1. Generate key pair \\((sk, pk=g1^sk)\\).  
2. Sign message \\(m\\), output \\(σ = H(m)^sk\\).
3. For aggregation, compute weights \\((t_1,...,t_n) = H1(pk_1,...,pk_n)\\) for all public keys. 
4. Compute aggregated signature \\(σ = σ_1^{t_1} * ... * σ_n^{t_n}\\).
5. For verification, compute \\((t_1,...,t_n) = H1(pk_1,...,pk_n)\\), and aggregated public key \\(apk = pk_1^{t_1} * ... * pk_n^{t_n}\\).
6. Check \\(e(g_1, σ) = e(apk, H(m))\\), equal if valid.

This scheme prevents public key substitution attacks, and is theoretically secure based on the co-CDH assumption. It retains the advantages of BLS signature aggregation, without requiring proof of secret key knowledge. Thus it is a good threshold BLS signature improvement. 

<br>

The threshold BLS signature mechanism is very suitable for blockchain systems. It not only reduces transaction size and storage, but also enables batch transaction verification for further performance gains. Threshold BLS signatures enable non-interactive aggregation, providing more flexibility. In the future, combined with aggregate public key proofs (POP), it can completely hide users' public key information.

As blockchain enters a new phase of large-scale commercialization, various efficiency and privacy technologies will become increasingly important. Threshold BLS signatures provide a very practical tool.

<br>

The main works in BLS signature algorithms are curve pairing and signature aggregation.

Curve pairing requires a special function that maps two points on a curve to a number, satisfying the property that multiplying either point by an unknown x results in the same output. Such functions exist and do not leak any information about x (security). 

For verification, we just check if the mapping of the public key and hash of message (two points on the curve) equals the mapping of the curve generator and signature (two other points). If so, the BLS signature is valid.

Of course, BLS signatures are not perfect. Their complexity is an order of magnitude higher than ECDSA. When verifying the aggregated signature of 1000 transactions in a block, 1000 pairings are still required, possibly slower than verifying 1000 separate ECDSA signatures. The only benefit is fitting more transactions per block, since aggregated signatures are just 32 bytes. There is also a MOV attack on elliptic curve cryptosystems using the pairing function to compromise security.

<br>

A comparison of ECDSA, Schnorr and BLS signature algorithms:

|                               | ECDSA                         | Schnorr                                                 | BLS                                           |
| ----------------------------- | ----------------------------- | ------------------------------------------------------- | --------------------------------------------- |
| Verifying multiple signatures | Each signature and public key | Aggregated signature and public key per transaction [1] | Aggregated signature and public key per block |
| Random number generation      | Specify random point          | Relies on RNG                                           | No RNG needed                                 |
| Signer communication          |                               | Required                                                | Not required                                  |
| Signature length              | 320 bits                      | 320 bits                                                | 160 bits                                      |

[1] Schnorr signatures can aggregate all signatures and public keys in a transaction into a single signature and public key, indistinguishable from individual ones. Verifying the aggregated signature speeds up block verification.

<br>

