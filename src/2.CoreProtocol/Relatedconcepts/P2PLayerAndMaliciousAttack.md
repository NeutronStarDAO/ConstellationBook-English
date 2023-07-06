# How does the P2P layer reduce malicious attacks?

The P2P layer uses several methods to mitigate the effects of malicious behavior and ensure that replicas in a subnet can communicate efficiently and securely. Here are some key points on how the P2P layer handles potential malicious behavior:

**Use encrypted hashes to ensure integrity**: When a replica receives a notification of an artefact, the notification contains an encrypted hash of the artefact. After downloading the artefact, the replica will apply the same encrypted hash function to the downloaded content. Only if the generated hash matches the hash in the notification is the artefact considered valid and continues processing. This prevents malicious replicas from sending tampered artefact content.

**Client verification**: Even if a malicious replica sends an artefact with a matching hash, client components (such as consensus layer components) need to verify the artefact before processing or forwarding it to other replicas. This includes verifying signatures or checking that the artefact conforms to expected formats and rules.

**Selective download of artefacts**: The P2P layer does not download artefacts immediately upon receiving a notification. Instead, replicas decide whether to download an artefact based on the notification content and their own state. This reduces the impact of malicious replicas flooding the network with unnecessary or malicious artefacts.

**Redundancy and fault tolerance**: When downloading large artefacts that can be divided into multiple data blocks, the P2P layer will try to download data blocks from multiple replicas that have published the artefact. This improves download speed and bandwidth utilization. At the same time, it provides fault tolerance in the event of malicious behavior or failure of a replica.

**Verified connections**: The underlying transport component of the P2P layer establishes secure TLS and TCP-based connections between replicas in a subnet. Replicas use private keys to authenticate each other to ensure that only replicas in the same subnet can communicate. This reduces the risk of unauthorized replicas injecting malicious traffic into the subnet.

**Monitoring and resending requests**: The P2P layer continuously monitors connection quality and received artefacts. If a replica encounters an artefact issue, such as lost or invalid data, it can request the peer replica to resend the artefact. This mechanism helps recover from issues that malicious replicas may cause.