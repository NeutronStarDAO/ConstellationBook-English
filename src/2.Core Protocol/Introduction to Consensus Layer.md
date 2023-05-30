# Introduction to Consensus Layer

# What? PoUW? Isn't it Pow?

Consensus on IC ? Take a look at this term: PoUW, Proof of Useful Work.

------

Does it looks familiar to you?

![image-20230131200036914](https://github.com/NeutronStarDAO/ICCookBook-Chinese/raw/main/src/2.%E6%A0%B8%E5%BF%83%E5%8D%8F%E8%AE%AE/assets/2.%E5%85%B1%E8%AF%86%E5%B1%82/image-20230131200036914.png)

Proof of Work (PoW) is the consensus algorithm of Bitcoin, which is very inefficient from today's perspective, but still it is relatively secure. What is Proof of Work ? Imagine a school organizing an competitive exam, only students who get full marks are eligible to put their names on the "Honor Wall" of the Education Bureau, they will be also rewarded with one full bitcoin! The school is open 24 hours a day, anyone can come and take the exam at any time, and the results will be released automatically after finishing all the questions. After one student gets full marks, the answer sheets of students who are still taking the exam will be immediately invalidated. That is because the answer (full mark paper) has already been born. In addition,other students must copy the full mark paper and then start the next exam.

In the school of PoUW, the candidates are randomly divided into several classes, and the exams are conducted in these classes. This time,not everyone can enter the school but only teachers can join. Each class completes a set of questions together. Since everyone is supposed to be an experienced teacher, they will roll the dice to decide who gets to do the first question, and who gets to do the second ... After finishing the questions, everyone has to discuss and reach a consensus on various opinions, and then hand in the papers eventually. Each person in the class shares the reward equally.

How efficient it is!😉😎

PoUW adds a 'U' to PoW, it significantly improves performance and reduces useless work for node machines. PoUW does not artificially create difficult hash calculations. It tries to put as much computing power as possible into serving users of the network. Most resources (CPU, memory) are used to execute codes in Canisters.



## How is consensus reached?

No matter what, Bitcoin is one of ancestors of the blockchain. Even though the consensus is inefficient, it is a solution to distributed system issues.

Satoshi Nakamoto's Bitcoin is a feasible solution to the "Byzantine generals problem".

Simply put, this problem involves attempts to reach consensus on a course of action through information exchange in an unreliable network with potential threats. Satoshi Nakamoto's solution used the concept of Proof of Work to reach consensus without a centralized authority that needs to be trusted, representing a scientific breakthrough in distributed computing and transcending the widespread applicability of currency.

However, can consensus only be reached in this way? Is there a more efficient way with high security?

Let's talk about the essence of consensus.

The essence of consensus is to maintain data consistency in a globally distributed network.

Bitcoin's approach is that everyone competes for computing power to determine who gets to pack valid blocks, then everyone has to copy the winner's blocks. In this way, Bitcoin transaction ledgers will have multiple copies, and the goal of keeping data consistent across all nodes is achieved. However, the efficiency is very low.

Let's take a look at the logic of consistency here:

The goal: Maintain data consistency across all nodes.

The method: Rely on some means to select a node to pack the block, and others copy the block packed by that node. The same node cannot be selected repeatedly in a continuous way, and the process of getting selected to pack the blocks is random.

Let us analyse the purpose only. Since the purpose is to maintain data consistency between nodes, as long as the nodes receive messages at the same time, the problem will be solved.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/fe3ed3d7-ae5b-43e2-8436-b06815e15c40)


In the actual network environment ,it could not be achieved. Information transmission always has varying delays, not to mention that nodes are not located in a same place. The locations of clients are also far and near,the transmission distances are different. It is chaotic and sounds impossible to guarantee that all nodes receive messages at the same time. What should we do?

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/f162c208-3fbe-41c0-85fc-fd8381775760)

The answer is simple. Building a "relay station" can solve this problem. No matter where the messages come from, they are first queued in the relay station, then the relay station sends the messages and execution order to the nodes. As long as the nodes execute the operations in the order received from the relay station, data consistency can be guaranteed!

It is not over yet! Since there is another big issue: Centralization. All nodes have to obey the commands of the relay station. If the relay station says to execute messages in the order of ABDC, the nodes have to follow ABDC. Going round and round a big circle, the result returns to its starting point. So how can we sort messages in a decentralized way?

Decentralizing is actually simple, that is, doing something completely independent of any other one. There is no "boss" or "manager".It is very democratic,  everyone works together to reach a consensus. Anyone who comes is allowed to pick up the jobs, and anyone who leaves will not affect the continuous operation of the system. (Unless everyone leaves, but with economic incentives, someone will always come!)

So how can we design it? The nodes need to reach consensus on the order of executing messages and we need to decentralize the working process of the relay station.

IC is designed like this: (IC abstracts nodes into replicas in subnets)

We could not rely on a relay station. Although the time for messages to arrive at each replica may be different (that is, the time of executing messages is different), all replicas must execute messages in the same order.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/6748452b-c774-40f5-bd91-d28a6ed85520)

Then if everyone's order is different, which order should be executed? It will use random number to decide! (IC's Random Beacon)

IC uses a verifiable random function (VRF) at the base layer. It can generate unpredictable random numbers, and everyone can verify that the random numbers are not made by human.

VRF uses threshold BLS signature scheme. The threshold BLS signature algorithm uses DKG to distribute private key shares to replicas. This is a non-interactive distributed key generation protocol. DKG can distribute private key shares between members. There is no need for a trusted third-party, it does not depend on any member to distribute private key shares, avoiding single points of failure. Everyone uses the private key shares to sign information. Once the signature reaches the threshold, it can be aggregated into a complete signature. The signature process is non-interactive. Any third party can aggregate after receiving enough shares. Anyone can verify the signature with the unique public key. The public key is also recorded in the NNS registry.

If a message is confirmed, no matter which private key shares participate in the signature, as long as the threshold quantity is reached (the threshold for generating the Random Beacon is one-third), the final unique signature information can be aggregated. For example, the threshold in the following figure is 6. In order for the 16 replicas to generate the random beacon signature for this round, as long as the signature is greater than 6, it can be aggregated.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/91d33324-818d-4a12-9d88-8a6a63e884dd)

As long as the bad guys get less than one-third of the private key shares, they cannot interfere with the threshold BLS signature. It is also impossible to predict the signature result because the private key shares are not enough. That is to say, no one knows the signature result.

> The consensus of the subnet can resist up to less than one-third malicious replicas. If the malicious replicas are less than one-third, all they can do is sign or not sign, they are unable to interfere with the final result of the threshold signature, nor prevent the signature from being generated. If malicious replicas are greater than or equal to one-third, the subnet has been destroyed, so the random number does not matter. Therefore, the threshold for the random beacon is low, at one-third. The speed of generating random numbers can be faster.

- In the traditional RSA algorithm, you control the private key and the message is public, which is equivalent to knowing the signature result yourself. After the private key is leaked, others can also know the signature result in advance.
- However, in the threshold BLS  signature algorithm, a group of people control the private key shares. The signer himself does not have the complete private key, so he does not know the signature result. Only after everyone has signed and aggregated ,then they know the signature. Throughout the process, no one knows the global private key, but the signature result is the result recognized by most people. A group of people generate signatures, and no individual can predict the signature result. A single person cannot prevent the signature from being released.

This can serve as Random Beacon to provide a reference for which replica to produce a block. This random number is also a consensus result and cannot be tampered with by a single person. Moreover, this can continuously safely generate random numbers, as long as different information is used for each round of signatures. This different information is naturally the random beacon and some block dkg_ids of the previous round, so that the information signed in each round is different.

IC's random beacon, notarization, finality, random tape, and certified copy status all use threshold BLS signatures. (The random tape and certified copy status are content at the execution layer)



## Solution

Let us take a closer look on how IC's consensus protocol produces a block

### Preparation before block

The consensus protocol produce block by rounds. For example, consensus is reached on the genesis block in round 1, and round 6 for block 6.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/f31ef6bf-b877-4548-9fdc-143f2f8e4685)

Before start, the subnet first randomly selects some replicas to form a "consensus committee" according to the number of replicas. If the number of replicas is too low, all replicas will join the committee. The members in the committee are responsible for producing the blocks, so even if there are a large number of replicas in the subnet, it will not affect performance.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/6af04a3f-b9b1-44b5-b0ff-87fcfddbe9ee)

There is also the concept of "epoch" in the subnet. An epoch is approximately a few hundred rounds. The NNS can adjust the epochs for each subnet.

Each subnet operates within epochs that contain multiple rounds (typically around a few hundred rounds). Different replicas make up the committees in each epoch.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/47e1f2e9-5717-4b5d-b1a2-1b788b10803b)

At the end of each epoch, the consensus committee members for the next epoch are selected through the random beacon, and the current consensus committee members will all become random beacon committee members in the next epoch.

The first block of the new epoch contains the list of consensus committee members and random beacon committee members for this epoch.

And at the beginning of the new epoch, the private key shares will be redistributed to the members. This process is called Pro-active resharing of secrets. There are two reasons for doing this:

- When the members of the subnet change, resharing can ensure that any new member will have new private key shares, and any member exiting the subnet will not have a new private key share.
- Even if a small amount of private key shares are leaked to attackers in each epoch, it will not threaten the consensus.

The number of consensus committees is related to the total number of replica members in the subnet. To improve scalability, in small-scale networks, committee members can be all replicas. In large-scale networks, committee members are part of all replicas and constantly changing in each epoch.

The number of consensus committee members cannot be too large or too small. Too few are insecure, too many affect the consensus speed.

So the relationship between the number of committees and the total number of members has a mathematical model to describe: When the total number of members in the subnet tends to infinity, the hypergeometric distribution tends to the binomial distribution, that is, non-replacement random sampling tends to replacement random sampling. Because the total number of replica members is infinite, there is no difference between replacement and non-replacement. If you are interested, you can read the introduction **here**.

Once the preparation is complete, blocks can be produced.



### Block Maker

At the start of each round, the random beacon generated from the previous round produces a ranking to determine the weight of members to produce blocks. The leader with the highest weight is given priority to produce blocks. (As shown in the figure below, the ranking assigns a number from 0 to 4 to the 5 consensus committee members, with 0 having the highest weight)

Under normal circumstances, the leader is honest and the network connection is normal. The leader is responsible for producing blocks. Others are waiting to notarize the leader's block. Even if block from the 2nd maker is received, it will not be notarized until the leader's block is received.

At the same time, the random beacon committee will also package, sign and broadcast the hash of the previous round's beacon and the NiDKG record of this round. When the signature reaches the threshold, this round's random beacon is generated, which also determines the block production weight for the next round.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/6686cda7-0c23-4ae3-aa95-f2f7dffd836b)

A non-genesis block generally contains:

- The messages received from the time the previous block was notarized to the time this block was packaged, called the "payload".
- The hash of the previous block.
- The ranking of the block-producing replica.
- The height of the block.

After the block is assembled, the replica responsible for producing the block will generate a **block proposal**, including:

- The block itself.
- Its own identity.
- Its own signature on this block.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/e8ad34e1-b042-46b8-af32-4da9f28d88ac)

Then broadcast the block proposal to other members.

The leader produces a block and broadcasts it to everyone. After notarization is completed, since only the leader's block is notarized, there is no need for finalization, and it enters the next round. This is the fastest and most common situation. About 1 second to finalize a block. (Optimistic responsiveness: The protocol will continue to execute based on actual network latency rather than the upper limit of network latency)

If after waiting for a period of time, the leader's block has not been received, it may be that the leader has a poor network or the machine has malfunctioned. Only then will members accept block from 2nd member or 3rd member and notarize their blocks.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/bb3cce3c-7016-4f4e-94b2-1b41c2c0ea21)

The system has an agreed upon waiting time. If the leader's block is not received within a period of time, it will expect block 2 in the second time period. Then in the third time period, block 3 is expected. If block 3 is its own, then produce the block itself ...



### Notarization

**Notarization only verifies the reasonableness of the block**, and the notarized block does not represent consensus. This ensures that at least one block in the current round can be notarized.

Therefore, notarization does not mean consensus, nor does it require consensus. If multiple blocks have the same weight, these blocks will be signed.

During notarization, the members of the consensus committee verify the following three aspects:

1. The block should contain the hash of the block already notarized in the previous round.
2. The payload of the block must meet certain conditions (specific regulations on the payload content, these conditions are independent of the consensus protocol).
3. The ranking of the replica responsible for producing this block must correspond to the ranking in the random beacon (for example, if the replica ranked second claims to be ranked first, then this block will not be notarized).

If the block information is correct, the replica responsible for verification first signs the block height and block hash, and then forms a "**notarization share**" with the just signed signature, hash, height, and its own identity. Broadcast the notarized shares.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/8e6bd975-3231-4078-b57f-bcac23ab2fa3)

Notarization also uses BLS threshold signatures. When a replica receives enough (the threshold is two-thirds) notarization share, it aggregates the signature share to form a notarization for this block.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/0bb81f7e-bcf8-4625-a835-833ace7da411)

The aggregated notarization information includes the block hash, block height, aggregated signature, and more than two-thirds of the identity identifiers. Replicas either find that they have collected enough notarization shares and aggregate them into notarizations themselves; or they receive aggregated notarizations from others.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/ebb8aa45-a9a7-412f-8c18-68b0c80f5ccf)

After notarization, the block is still broadcast. When other members receive the already notarized block, they re-broadcast the notarized block and do not generate notarization shares for other blocks.

For example, in the figure below, the girl holding the cell phone and the blue hat enter the next round of consensus. When the girl sends messages to the other three people, the network is interrupted for 700 milliseconds. The message forwarded by the little blue hat plays a key role. Otherwise, five missing three would not be able to work.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/0a476c14-a9ca-4a25-83e8-bea3d7eff1c8)

If the leader's block has a problem and the notarization fails, the weighting of the second block will now be the largest. If the blocks of the second and the third have both passed notarization, the leader of the next round will choose to block after the block with the greatest weight. As in rounds 5 and 6 below, the weight of the second block is greater than the weight of the third block. Adding up the weights of all blocks, the chain composed of yellow and purple blocks is the chain with the greatest weight.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/6a44e5fe-493d-4cb2-85c7-afd1a0c472f0)



### Finalization

Because sometimes more than one block may be generated (when the leader does not respond, the second and the third may produce block to save time). This requires a finalization. The finalization stage will determine the only block that everyone has notarized. Then the blocks before the block that everyone agrees with will also be implicitly finalized, and other branches will become invalid.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/a47ea62b-5d12-4b59-afd1-df442fab6669)

The finalization process is specifically:

After a replica finds a notarized block, it will start checking whether it has notarized any other blocks in this round. If it has not notarized any other blocks, it will broadcast a "finalization share" for the block. To prove that it has only issued a notarization share for this block.

To achieve the finalization of a block, two-thirds of different replicas need to issue finalization share, and then aggregate for the finalization of a block. The format of the finalization share is exactly the same as the notarization share (but marked in a specific way to prevent confusion). After receiving the finalized block, like the notarization process, it will broadcast to other members again.

Note: The replica does not deliberately wait for the aggregation of the finalization share into the finalized before entering the next round. After receiving a finalization share of a certain height, the replica only checks whether it has notarized blocks other than that block, and then broadcasts its own signed finalization share; or forwards the finalization share intact.

For example, after a replica receives the finalization share of round 10 in round 11, it will check its behaviour at that time and then broadcast a reply.

If after a while, if you receive the finalization of the block in round 10, you can implicitly consider all previous blocks as finalized.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/3142ef13-478b-4683-bbf4-10cea35414fa)

These finalized blocks can be considered as safe confirmed by everyone, meaning that all replicas agree with the branch where the finalized block is located. At the height of block 10, only this block has passed notarization. Then the replica reaches consensus at this height.

For example:

If a replica only generates a notarization share for one block in Round 5, the replica will also issue a finalization share and then enter Round 6 consensus. If the finalization is received later, the branch containing the finalization is considered valid. If the finalization does not fork afterwards, there will be no problem without finalization.

It is possible that in Round 4, half of the replicas generated notarization shares for the leader and the second blocks, and the other half of the replicas only generated notarization shares for the leader's block. Then the finalization share proposed by the replicas that only notarized the leader's block cannot reach the threshold and cannot obtain finalization. Only half of the replicas generate notarization shares for the second member's block, so the second member's block does not obtain notarization.

![image](https://github.com/NeutronStarDAO/ICCookBook-English/assets/89145158/3925837b-88e0-4d66-b75f-51ebe3794b74)

Compared with many other blockchains, the advantage of the IC consensus protocol is that it adopts asynchronous finalization. In other blockchains, nodes usually need to find the longest chain. If the chain forks, the nodes need to wait for a while to find the longest chain. If some blocks are missed due to network failures, the longest chain cannot be found, and data from other nodes needs to be synchronized.

The IC protocol does not rely on finding the "longest chain" to eventually confirm the block. IC's finality method only relies on cryptographic signatures, not on the confirmation of the entire chain. A small number of signatures can observe a block consensus formed without waiting for the confirmation of the entire chain. Forks can be eliminated in a short time, and finalization speeds can be achieved in less than one second.

**The consensus process is over here!**

To summarize, the members of the Consensus Committee need to do 3 things when entering a new round:

(1) See how they rank and then decide whether to produce block yourself

(2) Notarize the block

(3) Observe the blocks, find the main chain, and ignore invalid branches

The Consensus Committee will become a random beacon committee in the next period and be responsible for generating random beacons for each round.

The consensus process is for the leader to block, everyone verifies and then issues a notarization share, the notarization share reaches the threshold and aggregates into notarization, and enters the next round. Finalization does not have to be completed every round.

The IC consensus protocol ensures that when there are individual malicious attacks, IC's performance will flexibly decrease instead of directly freezing. The consensus protocol currently tends to maximize performance as much as possible in the "optimistic case" without failures.

As the protocol progresses round by round, the blocks connected from the genesis block form a chain that extends continuously. Each block contains a payload, consisting of a series of inputs and the hash of the parent block.

Honest replicas have a consistent view of the path of the blockchain. The blocks record the messages that have been sorted, which are sent by the message routing layer to the execution layer for processing.



## Source Code

https://github.com/dfinity/ic/tree/master/rs/consensus/src/consensus

### random_beacon_maker

Threshold BLS signature



### on_state_change

If it meets the requirements for proposing random beacon sharing, propose it.

It contains a function called `on_state_change`. This function accepts a parameter pool of type `PoolReader` and returns a value of type `Option<RandomBeaconShare>`. The function of this function is to obtain the current node information and the information in the pool, and determine whether the current node needs to generate a random beacon based on this information. If a random beacon needs to be generated, generate the beacon and return it encapsulated in the `RandomBeaconShare` structure.

The function first obtains the ID and current height of the current node, and then obtains the random beacon corresponding to the height from the pool. Next, it gets the next height and tries to get the random beacon corresponding to that height from the pool. If the pool does not have this random beacon and the current node belongs to the random beacon committee of that height, generate the random beacon and return it.

The process of generating a random beacon involves creating a `RandomBeaconContent` structure, which contains the height and hash value. Then, the function will call a function named `active_low_threshold_transcript` to get the transcript of the current height. If the transcript can be successfully obtained, the `crypto.sign` method is called to sign the `RandomBeaconContent` structure. After the signature is successful, the function will return the signature information encapsulated in the `RandomBeaconShare` structure. If any step has a problem, the function will return `None`.

```rust
pub fn on_state_change(&self, pool: &PoolReader<'_>) -> Option<RandomBeaconShare> {
    trace!(self.log, "on_state_change");
    let my_node_id = self.replica_config.node_id;
    let height = pool.get_notarized_height(); // get notarized height of blocks 
    let beacon = pool.get_random_beacon(height)?; // get a random beacon(From pervious round)
    let next_height = height.increment(); // That is the height of the current round (everyone is signing blocks, no decisive blocks have been made, so it's called "next block").
    let next_beacon = pool.get_random_beacon(next_height); // Get the random beacon for the next block height.
    match self.membership.node_belongs_to_threshold_committee(
        my_node_id,
        next_height,
        RandomBeacon::committee(),
    ) {
        Err(MembershipError::RegistryClientError(_)) => None,
        Err(MembershipError::NodeNotFound(_)) => {
            panic!("This node does not belong to this subnet")
        }
        Err(MembershipError::UnableToRetrieveDkgSummary(h)) => {
            error!(
                self.log,
                "Couldn't find transcript at height {} with finalized height {} and CUP height {}",
                h,
                pool.get_finalized_height(),
                pool.get_catch_up_height()
            );
            None
        }
        Ok(is_beacon_maker)
       // This code uses a match expression that matches a tuple containing two elements.
// The first element is a boolean indicating whether a new random beacon needs to be created for the next height;
// The second element is the next height.
//
// If the first element is true, the first arm is executed, otherwise the second arm is executed.
//
// In the first arm, if the current node is a beacon maker and no random beacon has been generated for the next height
// and the current node has not obtained a share of the random beacon for the next height, then create a random beacon.
// In the second arm, return None.
            if is_beacon_maker
                && next_beacon.is_none()
                && !pool
                    .get_random_beacon_shares(next_height)
                    .any(|s| s.signature.signer == my_node_id) =>
        {
            let content =
            // Hash the previous random beacon
            RandomBeaconContent::new(next_height, ic_types::crypto::crypto_hash(&beacon));
// There is an issue of whether it is appropriate to use the dkg_id of the genesis block h
// to generate a random beacon for height h. The reason this is possible is that we actually only generate
// a random beacon for height h after a block at height h exists, and we only use the random
// beacon for height h-1 when verifying the block at height h. Therefore, using the dkg_id
// of the genesis block to generate the random beacon will not affect the validity and correctness of the blocks,
// because in fact the random beacon is only used after the blocks are generated to determine the ranking
// of replica block generation in the next round.
            if let Some(transcript) =
                active_low_threshold_transcript(pool.as_cache(), next_height)
            {
                match self.crypto.sign(&content, my_node_id, transcript.dkg_id) {
                    Ok(signature) => Some(RandomBeaconShare { content, signature }),
                    Err(err) => {
                        error!(self.log, "Couldn't create a signature: {:?}", err);
                        None
                    }
                }
            } else {
                error!(
                    self.log,
                    "Couldn't find the transcript at height {}", height
                );
                None
            }
        }
        _ => None,
    }
}
```

### get_notarized_height

It has no parameters. The function returns a `Height` value.

The purpose of this function is to get the maximum height of the notarized blocks. The following is the implementation of this function:

First, use the `get_catch_up_height` method to get the highest catch-up package height that has not been processed, and store it in the `catch_up_height` variable.

Then, extract all verified notarizations from the pool and use the `max_height` method to get the maximum height of the notarized blocks. If no notarized blocks are found, return `catch_up_height`.

Finally, select the larger value from `catch_up_height` and the maximum height of the notarized blocks, and return it as the result.

```rust
pub fn get_notarized_height(&self) -> Height {
    let catch_up_height = self.get_catch_up_height();
    self.pool
        .validated()
        .notarization()
        .max_height()
        .unwrap_or(catch_up_height)
        .max(catch_up_height)
}
```

### get_random_beacon

It has two parameters: `self`, which is a reference to an object instance, and `height`, which is a `Height` value. The function returns an `Option<RandomBeacon>`.

The purpose of this function is to get a valid random beacon at a given height. The following is the implementation of this function:

First, use the `cmp` method to compare the given height with the height returned by the `get_catch_up_height` method. `get_catch_up_height` returns the highest catch-up package height that has not been processed.

If the given height is less than the height returned by `get_catch_up_height`, it means that the random beacon at that height has not been generated yet, returning `None`.

If the given height is equal to the height returned by `get_catch_up_height`, it means that the random beacon at that height has been included in the highest catch-up package, so extract the random beacon from the catch-up package and return it wrapped in `Some`.

If the given height is greater than the height returned by `get_catch_up_height`, it means that the random beacon at that height has been verified in the pool. Therefore, extract the verified random beacon from the pool, and then use the `get_only_by_height` method to obtain the random beacon by the given height. If it can be obtained, return it, otherwise return `None`.

```rust
pub fn get_random_beacon(&self, height: Height) -> Option<RandomBeacon> {
    match height.cmp(&self.get_catch_up_height()) {
        Ordering::Less => None,
        Ordering::Equal => Some(
            self.get_highest_catch_up_package()
                .content
                .random_beacon
                .as_ref()
                .clone(),
        ),
        Ordering::Greater => self
            .pool
            .validated()
            .random_beacon()
            .get_only_by_height(height)
            .ok(),
    }
}
```

### active_low_threshold_transcript

It has two parameters: `reader`, which is a reference to an object implementing the `ConsensusPoolCache` trait, and `height`, which is a `Height` value. The function returns an `Option<NiDkgTranscript>`.

The purpose of this function is to get the active low threshold NiDkg transcript for a given height. The `get_active_data_at` function is called and passed `reader` and `height` as parameters. The return value is mapped to extract the `low_threshold_transcript` field of the returned data object.

If the `get_active_data_at` function returns None, the value of the entire expression is None. Otherwise, the `low_threshold_transcript` field will be extracted and returned wrapped in Option.

```rust
/// If found, return the current low transcript for the given height.
pub fn active_low_threshold_transcript(
    reader: &dyn ConsensusPoolCache,
    height: Height,
) -> Option<NiDkgTranscript> {
    get_active_data_at(reader, height).map(|data| data.low_threshold_transcript)
}
```

### get_active_data_at

If active DKGData is found at the given height, return that DKGData.

```rust
fn get_active_data_at(reader: &dyn ConsensusPoolCache, height: Height) -> Option<DkgData> {
// There is an issue to note when determining the active DKG data. When using the latest finalized DKG summary,
// there is a problem that the next block or CUP cannot be calculated when the batch lags consensus. This is because only when
// the final block reaches a certain height and points to at least the notarization state of that height can we create the CUP. To solve this problem, the comment
// proposes a solution: first try to use the CUP summary block to determine the active DKG data, and if it fails, try to use the
// newest finalized DKG summary block. This avoids the situation where the next block or CUP cannot be calculated.
    get_active_data_at_given_summary(reader.catch_up_package().content.block.get_value(), height)
        .or_else(|| get_active_data_at_given_summary(&reader.summary_block(), height))
}
```

### get_active_data_at_given_summary

Returns the active DKGData (DKG data) at the given height according to the given summary block. A summary block is a block at a fixed height that contains some metadata, e.g. public keys for verifying the next consensus proof, digests of DKG data, etc. The height of a summary block is typically a multiple of some regular interval, e.g. one summary block every 100 blocks.

The summary block contains digests of DKGDatas, including a field called active_data which points to the DKGData currently in use. This function first checks if the given summary block has an active_data field that matches the given height, and if so returns `active_data`, otherwise returns `None`.

```rust
fn get_active_data_at_given_summary(summary_block: &Block, height: Height) -> Option<DkgData> {
    let dkg_summary = &summary_block.payload.as_ref().as_summary().dkg;
    if dkg_summary.current_interval_includes(height) {
        Some(DkgData {
            registry_version: dkg_summary.registry_version,
            high_threshold_transcript: dkg_summary
                .current_transcript(&NiDkgTag::HighThreshold)
                .clone(),
            low_threshold_transcript: dkg_summary
                .current_transcript(&NiDkgTag::LowThreshold)
                .clone(),
        })
    } else if dkg_summary.next_interval_includes(height) {
        let get_transcript_for = |tag| {
            dkg_summary
                .next_transcript(&tag)
                .unwrap_or_else(|| dkg_summary.current_transcript(&tag))
                .clone()
        };
        Some(DkgData {
            registry_version: summary_block.context.registry_version,
            high_threshold_transcript: get_transcript_for(NiDkgTag::HighThreshold),
            low_threshold_transcript: get_transcript_for(NiDkgTag::LowThreshold),
        })
    } else {
        None
    }
}
```

### aggregate

Other replicas on sharing random beacon nominations:

```rust
/// This function is used to aggregate signature shares into complete artifacts.
///
/// Consensus will receive many artifact signature shares over its lifetime.
/// aggregate attempts to aggregate these signature shares into complete
/// signatures for the corresponding contents.
///
/// For example, aggregate may take a Vec<&RandomBeaconShare> and output a Vec<&RandomBeacon>.
///
/// The behavior of aggregate is as follows:
///
/// Group signature shares by content, producing a mapping from content to
/// vectors of signature shares, where each signature share signed the related content
/// For each (content, shares) pair, look up the threshold and determine if enough
/// shares are present to construct a complete signature for the content
/// If a complete signature can be constructed, attempt to construct the complete
/// signature from the shares
/// If a complete signature can be constructed, construct an artifact using the
/// given content and complete signature
/// Return all successfully constructed artifacts
///
/// Params:
/// artifact_shares - The vector of artifact shares, e.g. Vec<&RandomBeaconShare>
#[allow(clippy::type_complexity)]
pub fn aggregate<
    Message: Eq + Ord + Clone + std::fmt::Debug + HasHeight + HasCommittee,
    CryptoMessage,
    Signature: Ord,
    KeySelector: Copy,
    CommitteeSignature,
    Shares: Iterator<Item = Signed<Message, Signature>>,
>(
    log: &ReplicaLogger,
    membership: &Membership,
    crypto: &dyn Aggregate<CryptoMessage, Signature, KeySelector, CommitteeSignature>,
    selector: Box<dyn Fn(&Message) -> Option<KeySelector> + '_>,
    artifact_shares: Shares,
) -> Vec<Signed<Message, CommitteeSignature>> {
    group_shares(artifact_shares)
        .into_iter()
        .filter_map(|(content_ref, shares)| {
            let selector = selector(&content_ref).or_else(|| {
                warn!(
                    log,
                    "aggregate: cannot find selector for content {:?}", content_ref
                );
                None
            })?;
            let threshold = match membership
                .get_committee_threshold(content_ref.height(), Message::committee())
            {
                Ok(threshold) => threshold,
                Err(err) => {
                    error!(log, "MembershipError: {:?}", err);
                    return None;
                }
            };
            if shares.len() < threshold {
                return None;
            }
            let shares_ref = shares.iter().collect();
            crypto
                .aggregate(shares_ref, selector)
                .ok()
                .map(|signature| {
                    let content = content_ref.clone();
                    Signed { content, signature }
                })
        })
        .collect()
}
```

DKG Algorithm : https://github.com/dfinity/ic/tree/master/rs/crypto/internal/crypto_lib/fs_ni_dkg
The main code responsible for orchestrating the DKG (sending, receiving and verifying DKG messages and ensuring they make it into the blockchain):
https://github.com/dfinity/ic/blob/master/rs/consensus/src/dkg.rs

Random Beacon:
https://github.com/dfinity/ic/blob/master/rs/consensus/src/consensus/random_beacon_maker.rs
