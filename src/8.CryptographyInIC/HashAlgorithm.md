Hash algorithms, also commonly referred to as fingerprint or digest algorithms, are a fundamental and crucial type of algorithm.

The origin of hash algorithms can be traced back to the 1950s, designed to address the issue of ensuring information integrity verification.

In the transmission and storage of digital information, it is essential to guarantee that information has not been unlawfully altered. This process is known as data integrity verification. Initially, encryption algorithms were considered for achieving integrity verification, but encryption algorithms serve their own purposes and are not well-suited for integrity verification.

<br>

Therefore, in the 1950s, some cryptographers, such as Ralph Merkle, proposed the use of "manipulation detection codes" to verify information integrity. This method involves extracting a shorter fixed-length value, known as the hash value, from the original information using a function. If the information is modified during transmission, the extracted hash value will also change.

In this way, the receiving party only needs to run the hash function again on the received information and check if the extracted hash value matches the one provided by the sender to verify whether the information has been altered during transmission. This method is both simple and effective, and it does not require the transmission or storage of any keys.

<br>

Subsequently, researchers proposed improved hash function algorithms, such as MD5, SHA-1, etc., to counter intentional attacks. This made information integrity verification based on hash functions more reliable.

Over time, hash functions have been widely applied in various fields, including digital signatures, blockchain, data storage, etc., due to their unique "avalanche effect." They have become an indispensable component of modern information security infrastructure.

In summary, hash algorithms were designed to address the challenge of quickly and effectively verifying integrity in information transmission. They have evolved into an essential tool in the digital world, resolving a crucial trust issue.

<br>

Hash algorithms can map arbitrary-length binary plaintext into a shorter (usually fixed-length) binary string, known as the hash value. Different binary plaintexts are challenging to map to the same binary string.

A message digest refers to using a one-way hash function to extract a fixed-length ciphertext from the data that needs to be digested. This ciphertext is also called a digital fingerprint. The digital fingerprint has a fixed length, and different plaintexts will always produce different results when extracting the digest. However, the digest for the same plaintext is consistent. Since there are no restrictions on the plaintext used for generating the digest, and the digest is of a fixed length, some plaintexts will inevitably produce the same digest. This phenomenon is called "collision." To avoid collisions, hash functions must have good collision resistance, meaning it is impractical to find a collision within existing computational resources (including time, space, funds, etc.).



A notable feature of message digest algorithms is that during the input message process, even a slight change in the message, such as altering one bit in the input message binary data, will result in vastly different output results. Therefore, message digest algorithms are useful for detecting small changes in objects such as messages or keys. Three key characteristics of message digest algorithms can be inferred:

1. The input length for message digest algorithms is arbitrary, while the output length is fixed.
2. Given the input for message digest algorithms, calculating the output is straightforward.
3. Common message digest algorithms include MD5, SHA, SHA256, SHA512, SM3, etc.

<br>

Message digest algorithms are not encryption algorithms and cannot be used to protect information. However, they are commonly employed to store passwords securely. For example, when users log in to a website and need to be authenticated using a username and password, storing plaintext passwords directly in the website's backend poses significant risks in the event of a data breach. To mitigate this risk, websites can utilize the characteristics of hash algorithms. Instead of storing plaintext passwords, they store the hash values of user passwords. During login, the website compares the hash value of the entered password with the stored hash value. Even in the case of a data breach, it is challenging to deduce the original login password from the one-way hash value.

However, in some cases, if user passwords are weak, using simple strings like "123456," attackers can precompute hash values for such passwords, creating a mapping between passwords and hash values to facilitate cracking. To enhance security, websites often calculate hash values with added random characters (salt), separating the hash values from the passwords. This significantly increases security.

<br>

Message digest algorithms can be used to verify data integrity, but only when the digest and data are transmitted separately. Otherwise, attackers can modify both data and digest simultaneously to evade detection. Message Authentication Code (MAC) or Keyed Hash is a cryptographic function that extends the digest function with authentication. Only with the digest key can a legitimate MAC be generated.

MAC is typically used alongside ciphertext. While encrypted communication ensures confidentiality, it does not guarantee the integrity of the communicated message. If an attacker has the ability to modify ciphertext in Alice and Bob's communication, they can induce Bob to receive and trust forged information. However, when MAC and ciphertext are sent together, Bob can confirm that the received message has not been tampered with.

Any message digest algorithm can serve as the foundation for MAC, and one fundamental approach is based on a digest-based Message Authentication Code (Hash-based Message Authentication Code, HMAC). HMAC essentially intertwines the digest key and message in a secure manner.

<br>

<br>