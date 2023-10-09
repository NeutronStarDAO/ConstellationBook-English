## What is the NNS for?

As summarized when covering threshold cryptography in IC:

***The greatest innovation of the Internet Computer lies in its use of a series of complex cryptographic technologies. It has achieved self-consensus within the subnet, which can be perceived as a type of sharding technology. With chain-key cryptography, secure cross-subnet communication can take place between subnets. As it does not require global consensus across the entire network, the Internet Computer can achieve horizontal scalability by adding more subnets.***



Technically, IC can scale infinitely. More subnets can be added as needed. However, within each subnet, the membership is fixed when consensus is reached internally, because the BLS private key shards have already been distributed to the consensus committee members. Even if new replicas want to join a subnet, they have to "wait in line" until the next era starts before getting the private key shards. So for each subnet, membership needs to remain relatively stable, unlike Bitcoin where new miners can join anytime.

Additionally, IC's ability to keep creating subnets (shards) requires a place to keep track of all these subnets, otherwise bad actors could create fake subnets pretending to be part of IC. If subnets could be freely created, the cost of communication and trust between them would greatly increase, with layers of verification needed for every cross-subnet message. ＞︿＜ 

However, IC must also remain decentralized and open.



**So to enable massive computational capacity and ultra low-cost cross-subnet communication while preserving decentralization, IC chose a compromise - the DAO.** If you are unfamiliar with what a DAO is, you can check [here](DAO.md) first.

<div class="center-image">
<img src="assets/NNS/image-20230708141930460.png" alt="img" style="zoom: 20%;" />
</div>

After careful consideration, the Dfinity team decided to create a "super subnet" within IC called the system subnet. This subnet has the highest authority in IC, acting as the super admin: creating subnets, deleting subnets, adding new nodes to subnets, splitting subnets, upgrading subnet protocols, upgrading replica software versions, adjusting Cycles exchange rates, managing unique Canister IDs, user Principal IDs, public keys for each subnet, and other critical parameters of the entire IC blockchain system. These key settings are determined or voted on internally by the super admin.

This hybrid model of embedding a DAO at the base layer of a blockchain system is called a DAO-controlled network. The entire IC network runs under the DAO's control.



The DAO that governs the whole IC system has a cool name: the Network Nervous System (NNS). Critical parameters of IC are decided by a DAO, which is the entire IC community. The community can decide to scale up by adding more subnets when needed! 😎

Anyone can use their neurons staked in the NNS to propose expanding capacity. Once passed, the NNS automatically spins up new subnets to handle network load, all without any downtime. The scaling process is invisible to users and developers. This differentiates IC from traditional blockchains - IC's TPS can increase with more subnets, simply by adding shards! This gives IC similar capabilities to traditional networks, where TPS improves by adding more servers.



One thing to note though, a DAO isn't built overnight. A DAO can't instantly become decentralized. It requires a gradual process, slowly decentralizing, subtly and silently. Like Bitcoin was tiny and fragile initially, surviving crises and hard forks before becoming what it is today.

> Bitcoin had bugs like overflows, taking others' coins, etc

The NNS is IC's super admin. If the major voting power of this DAO falls into bad actors' hands, the entire IC system is at risk, since the DAO is integrated at the base layer. The NNS subnet needs maximum security, so it has a very [large number of nodes](https://dashboard.internetcomputer.org/subnet/tdb26-jop6k-aogll-7ltgs-eruif-6kk7m-qpktf-gdiqx-mxtrf-vb5e6-eqe), making it difficult for hackers to control enough nodes and ensuring the NNS subnet's underlying security.

<div class="center-image">
<img src="assets/NNS/image-20230921102952706.png" alt="img" style="zoom:73%;" />
</div>

Currently there are 40 node machines. If one fails, no biggie:

<div class="center-image">
<img src="assets/NNS/image-20230921103600826.png" alt="img" style="zoom:67%;" />
</div>

Deployed within the NNS subnet is a sophisticated engine: the DAO's smart contracts.

> Oh right, on IC smart contracts are virtual containers called Canisters. They're like Docker or Kubernetes containers, but for Wasm code instead of images or pods. Pretty futuristic! Check out this link to learn more about Canisters if you haven't already. They're pretty neat.

I aimed to give the translation an authentic, conversational style that flows naturally for English speakers. I took a few liberties to inject some light humor around the comparisons to familiar technologies like Docker and Kubernetes, while staying true to the original meaning. Please let me know if you would like me to modify the translation at all - I'm happy to adjust the style and tone as needed.



## Exploring the NNS Internals

The NNS subnet currently has a total of 11 Canisters, which can be seen on the [Dashboard](https://dashboard.internetcomputer.org/canisters?s=25&subnet=tdb26-jop6k-aogll-7ltgs-eruif-6kk7m-qpktf-gdiqx-mxtrf-vb5e6-eqe).

|        Name        |         Canister id         |              Controller               |           Function           |
| :----------------: | :-------------------------: | :-----------------------------------: | :--------------------------: |
|    NNS Registry    | rwlgt-iiaaa-aaaaa-aaaaa-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |           Registry           |
|   NNS ICP Ledger   | ryjl3-tyaaa-aaaaa-aaaba-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |     ICP token functions      |
|  NNS ICP Archive   | qjdve-lqaaa-aaaaa-aaaeq-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |    Stores Ledger history     |
|   NNS Governance   | rrkah-fqaaa-aaaaa-aaaaq-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |   Voting, neuron proposals   |
|    NNS Lifeline    | rno2w-sqaaa-aaaaa-aaacq-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |    Controls Root Canister    |
| NNS Cycles Minting | rkp4c-7iaaa-aaaaa-aaaca-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |    Converts ICP to Cycles    |
| NNS Genesis Token  | renrk-eyaaa-aaaaa-aaada-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |       Genesis neurons        |
|      NNS Root      | r7inp-6aaaa-aaaaa-aaabq-cai | rno2w-sqaaa-aaaaa-aaacq-cai(Lifeline) | Controls other NNS Canisters |
| NNS Front-End Dapp | qoctq-giaaa-aaaaa-aaaea-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |  Stores NNS front-end code   |
|    NNS SNS-WASM    | qaa6y-5yaaa-aaaaa-aaafa-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |   Records SNS information    |
|   (NNS Identity)   | qhbym-qaaaa-aaaaa-aaafq-cai |   r7inp-6aaaa-aaaaa-aaabq-cai(Root)   |       User identities        |

<br>

### Canisters on the NNS

Their functions are all closely related to the operation of the system:

1. Registry Canister: The registry canister. The entire system configuration of the IC is stored in this canister, such as which nodes belong to which subnet and the software version each node should run.
2. Ledger Canister: The ICP ledger canister. It stores the ICP balances of each principal id and the ICP transaction history.
3. Archive Canisters: Once the number of transactions is too large to be stored in a single canister, the transaction history is stored here.
4. Governance Canister: The governance canister. It receives and stores proposals, all of which are related to governing the IC network. The governance canister also tracks neurons to determine who can participate in governance voting.
5. Cycles Minting Canister: Responsible for burning ICP to mint Cycles. All Cycles on the IC are minted by this canister.
6. Root Canister: It is the controller of all other NNS Canisters and is responsible for upgrading them. The controller of a canister has the authority to delete the canister, upgrade the code, and stop the canister. However, the Root Canister cannot arbitrarily upgrade canisters, it must wait for the governance canister to vote through a proposal to upgrade a canister before calling the Root Canister to execute the upgrade.
7. Lifeline Canister: It is the controller of the Root Canister and is responsible for upgrading it. The only canister written in Motoko in the NNS. When there is a very serious bug in the Rust lower level libraries, this Motoko canister can be used to upgrade the Root Canister, and then the Root Canister upgrades other NNS canisters to recover the IC system.
8. Front-End Dapp: The NNS front-end canister.
9. Genesis Token Canister: Used to initialize neurons that existed before genesis. The governance recorded some neurons of investors, foundations, and early contributors.
10. SNS-WASM Canister: Manages SNS canister related content. It is responsible for creating, updating, and deleting SNS canisters. After voting approval, it installs the Wasm module into the canister of the SNS subnet.
11. User Identity Abstraction Canister: Records user identities.

<br>

### Registry Canister

The most important canister is the Registry Canister.

It records all subnets on the IC, as well as the public keys of the subnets, BLS threshold signatures on the subnet public keys, various node information, Cycle prices, firewall configurations, etc.

New nodes must first submit their identity to the NNS, and then can join the subnet after voting approval. All replicas will monitor the Registry Canister to obtain the latest configuration. Each replica responsible for packaging blocks must also put the latest configuration into the block.

In any large distributed system, failures of individual nodes due to hardware failure, network connectivity issues or node operator decisions to take nodes offline are unavoidable. If this happens, the NNS will select a standby replica to replace the failed replica in its subnet. The new replica then joins the subnet and syncs state with the existing replicas by catching up on blocks, before participating in subnet consensus.

<br>

### Ledger Canister and ICP Tokens

The ICP token is managed by the Ledger Canister, which stores two things: accounts and transactions. Accounts track the number of tokens held by a particular principal (an authenticated identity on the IC). Tokens can then be sent from one account to another, which is recorded in the transactions of the Ledger Canister.

On the NNS, ICP has three uses:

1. Anyone can purchase ICP, stake it to the NNS and participate in IC network governance. Staking and voting will yield ICP rewards.
2. Nodes that participate in governance and provide computing power will also receive ICP rewards.
3. ICP is convertible to Cycles, which is the fuel for canisters to perform computation, communication and storage.

So you see, ICP is not a utility token directly integrated into the lower level system. ICP is a smart contract deployed on the NNS subnet, while Cycles is the system's utility token. But in any case, the smart contract on the NNS is the most important part of the entire blockchain system, and is equivalent to a lower level component supporting the operation of the IC (just deployed at the application layer from an architectural perspective).

> A deeper question: If a DAO is hacked and a large amount of ICP is controlled by hackers, can NNS voting be used to change ICP transaction records and forcibly refund stolen ICP?



### Governance Canister

The Governance Canister is responsible for holding neurons and determining who can participate in governance. In addition, it stores proposals and related information, such as how many votes in favor, how many votes against, etc. If a proposal is adopted, the Governance Canister will automatically execute the decision, and no one can stop it. Finally, the Governance Canister will distribute rewards to the neurons that participated in voting and contributed to the decision making.



### Defining Canister IDs on the NNS

By the way, the Canisters in the NNS subnet should be the first Canisters deployed on the IC. The Canister IDs of these Canisters in the NNS are directly defined in the [code](https://github.com/dfinity/ic/blob/master/rs/nns/constants/src/lib.rs). The Canister IDs are generated from u64 type indexes, and Canisters can also be converted back to u64.

This code defines the basic information of each Canister in the NNS subnet, and is an important part of initializing the NNS subnet.

```rust
// Define the index of each Canister in the NNS subnet
pub const REGISTRY_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 0;
pub const GOVERNANCE_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 1; 
pub const LEDGER_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 2;
pub const ROOT_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 3;
pub const CYCLES_MINTING_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 4;
pub const LIFELINE_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 5;
pub const GENESIS_TOKEN_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 6;
pub const IDENTITY_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 7;
pub const NNS_UI_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 8;  
pub const SNS_WASM_CANISTER_INDEX_IN_NNS_SUBNET: u64 = 10;
pub const NUM_NNS_CANISTERS: usize = ALL_NNS_CANISTER_IDS.len();

// Define the ID of each Canister based on the index
pub const REGISTRY_CANISTER_ID: CanisterId = CanisterId::from_u64(REGISTRY_CANISTER_INDEX_IN_NNS_SUBNET);
pub const GOVERNANCE_CANISTER_ID: CanisterId = CanisterId::from_u64(GOVERNANCE_CANISTER_INDEX_IN_NNS_SUBNET);
pub const LEDGER_CANISTER_ID: CanisterId = CanisterId::from_u64(LEDGER_CANISTER_INDEX_IN_NNS_SUBNET);
pub const ROOT_CANISTER_ID: CanisterId = CanisterId::from_u64(ROOT_CANISTER_INDEX_IN_NNS_SUBNET);  
pub const CYCLES_MINTING_CANISTER_ID: CanisterId = CanisterId::from_u64(CYCLES_MINTING_CANISTER_INDEX_IN_NNS_SUBNET);
pub const LIFELINE_CANISTER_ID: CanisterId = CanisterId::from_u64(LIFELINE_CANISTER_INDEX_IN_NNS_SUBNET);
pub const GENESIS_TOKEN_CANISTER_ID: CanisterId = CanisterId::from_u64(GENESIS_TOKEN_CANISTER_INDEX_IN_NNS_SUBNET);
pub const IDENTITY_CANISTER_ID: CanisterId = CanisterId::from_u64(IDENTITY_CANISTER_INDEX_IN_NNS_SUBNET);
pub const NNS_UI_CANISTER_ID: CanisterId = CanisterId::from_u64(NNS_UI_CANISTER_INDEX_IN_NNS_SUBNET);
pub const SNS_WASM_CANISTER_ID: CanisterId = CanisterId::from_u64(SNS_WASM_CANISTER_INDEX_IN_NNS_SUBNET);
```

<br>

### No Gas on NNS

The NNS is a system subnet where deployed Canisters are all related to the DAO and system operation. Developers cannot deploy Canisters on the NNS. So there is also no gas fee and no Cycles consumed on the NNS.

<br>

## NNS Governance

As a DAO, anyone can participate in voting to govern the network on the NNS without permission.

<br>

### Introduction to Neurons

Neurons are essentially the governance tokens of the NNS DAO.

By purchasing some ICP, sending it to the NNS wallet, and staking the ICP, you can obtain neurons. As a neuron holder, you can vote on proposals related to the IC network in the NNS, and also make proposals for others to vote on. The more ICP staked, the greater the voting power of the neuron. The longer the staking duration and the more votes cast, the voting power of the neuron will also increase. Of course, when the neuron matures, the additional ICP rewards received will be greater as well. To earn higher returns, neuron holders will tend to have their neurons participate in voting as much as possible to earn maximum voting rewards. At the same time, they will vote to support proposals that they believe will be most beneficial to the development of the IC network.

The system will distribute rewards in the form of additional ICP based on the "maturity" of the neurons (locked time). However, since the market price of ICP will fluctuate over time, the final returns of the neurons will also fluctuate.

If you don't know whether to vote in favor or against, you can also choose to follow a few neurons that you trust. When following other neurons, after the neuron votes, your neuron will cast the same vote. This way you don't have to open the NNS wallet to vote every day. Also, some proposals involve a lot of expertise and require analysis by experts in the community, which is difficult for ordinary people to decide on.

Neurons have the following key attributes:

* Utility tokens (ICP): These are the tokens used to create neurons. The amount of ICP locked in a neuron determines its base voting power.
* Dissolve delay: This determines the time the ICP will be locked in this neuron account. The delay can be set at creation, e.g. 6 months or 1 year.
* Maturity: This reflects the governance participation rewards earned by the neuron holder. Maturity can be reinvested into governance to gain more voting power, or withdrawn as ICP.

Here are more details about neuron staking and voting rewards. You can use the staking calculator to estimate returns if you plan on staking some ICP.

Each proposal has a defined voting period. At the end of the voting period, if a simple majority votes in favor of the proposal and the votes in favor exceed 3% of the total voting power, the proposal will be adopted. Of course, if an absolute majority (over half of the total votes) votes in favor or against a proposal, the proposal will be immediately passed or rejected. After a proposal is rejected, the proposer will lose 10 ICP, a measure in place to deter junk proposals.

> Otherwise anyone could initiate all kinds of meaningless proposals

Once a proposal is adopted, the Governance Canister will automatically execute the decision. For example, if a proposal proposes a change to the network topology and is adopted, the Governance Canister will call the Registry Canister to update the configuration.

For example, upgrading replicas through the NNS. First, an NNS proposal needs to be made (BlessReplicaVersion (#NodeAdmin)) to add a new replica version to the list. Then another NNS proposal is made to upgrade each subnet to the new version.

Once a proposal is adopted, the governance system will trigger an upgrade of the replicas in the subnet. The subnet's consensus layer will then autonomously decide when to execute the upgrade based on protocols between the nodes in the subnet:

These nodes have built-in support to download and apply upgrade packages without manual intervention.

The upgrade package contains the entire software stack required to run the nodes. After verifying the package contents correspond to the version the community voted to run, nodes will automatically restart into the new version.



### Proposal Mechanism on the NNS

ICP supports all kinds of proposal topics, not limited to one aspect. For example:

* Subnet management proposals: Consider changes in topology, such as adding or removing nodes.
* Node management proposals: Regarding management of node servers, such as upgrading software versions.
* Incentive mechanism proposals: Regarding profit distribution schemes of the blockchain.
* Opinion collection proposals: No direct execution, just to record community sentiment.

Next let's talk about the submission and processing flow for proposals:

Users who hold governance tokens ("neurons") can submit proposals. To prevent proposal spam, a fee of 10 ICP must be paid during submission. This fee will be refunded if the proposal is eventually adopted, otherwise it will not be returned.

Let's say I control a neuron, and I want to propose adding two compute nodes, called Node1 and Node2, to a subnet. I can submit a proposal, specifying my neuron ID, proposal type, and the main content being the suggested new nodes, with parameters Node1 and Node2. The on-chain governance program will first verify that I do indeed own this neuron, and that this neuron has normal voting rights. After verification, my proposal will be formally submitted and added to the governance program.

A legally submitted proposal will be stored by the governance program. In addition, the governance program will also calculate and record related information about the proposal, such as the voting power each neuron has for this proposal, and the aggregated total voting power of the proposal.

When a new proposal is added, the "votes in favor" count will be automatically increased by the submitter's own voting power, indicating the submitter is considered to have voted in favor of their own proposal.

Each proposal also has a voting period that indicates the time period voting can take place for the proposal.



### How can voters view and discuss proposals?

All proposals and related information can be seen on ICP's official Dashboard. Community members can discuss governance proposals anywhere, and many important proposals will also have public discussions on developer forums.



### What is the specific voting process?

After a proposal is added to the governance program, other users with neurons can then vote on the proposal. Currently the most convenient way to vote is through an official voting Dapp. Before voting, users need to first understand the currently open proposals available for voting.

If a neuron casts a vote in favor for a proposal, its voting power will automatically be counted towards the proposal's "votes in favor power". If it is a vote against, it will be counted towards "votes against power".

To encourage more users to participate, neurons can also choose to delegate their votes to other trusted neurons for voting. This mechanism is called liquid democracy. If users don't have time to vote themselves, they can also follow other authoritative neurons to vote and obtain a certain reward.



### Proposal Resolution and "Idle" Mechanism

A proposal can be resolved in two ways:

* Before the end of the voting period, if more than half of the total voting power votes in favor, the proposal is adopted; if more than half of the total voting power votes against, the proposal is rejected.
* When the voting period ends, if a majority voted in favor, and the votes in favor account for no less than 3% of the total voting power, then the proposal is adopted; otherwise, the proposal is rejected.

In addition, the governance algorithm also employs an "idle" mechanism, which extends the voting period when the vote is relatively balanced, allowing more time for discussion. Specifically, if the outcome of a proposal flips from in favor to against, or vice versa, the proposal deadline will be extended. The initial voting period for a proposal is 4 days, and can be extended up to 4 days, so the voting period can be between 4-8 days.

Once a proposal is adopted, the method defined in it will be automatically called and executed on the specified Canister.



### Voting Rewards

Participating in governance and voting can not only influence network decisions, but neuron holders can also obtain voting rewards. The rewards will accumulate in the form of "maturity".

Neuron holders have two ways to use maturity:

* Generate new neurons: The holder can choose to incubate a new small neuron to obtain ICP equivalent to the maturity amount. The new neuron only has a 7 day dissolve delay, allowing the ICP to be withdrawn in a short period of time.
* Reinvest maturity: The holder can choose to reinvest the neuron, continuing to lock the maturity until the neuron fully dissolves. This part of the maturity will be added to the neuron and increase its corresponding voting power. When the neuron fully dissolves, this part of maturity will be released along with the principal.



### How to avoid 51% attacks:

By design, most ICP is locked in neurons. Due to the dissolve delay, if an attack is initiated that damages the network, the price of ICP will be affected, resulting in significant value loss of the locked tokens.

In addition, the trading market for neurons can also have adverse effects. This is because attackers can cause panic leading to selloffs of neurons, allowing attackers to purchase large amounts of neurons on the cheap, negatively impacting network security.



## Service Nervous System (SNS)

Similar to how the IC is controlled by the NNS, decentralized applications deployed on the IC can also be controlled by a DAO. As mentioned earlier, the IC is like a cloud service, and Canisters deployed on it can be upgraded and have their code updated since they have controllers.

Currently, some Dapps are either controlled by the developer who deployed them, or are completely controller-less black hole Canisters. Neither situation is ideal. If a Dapp is controlled by a centralized development team, users have to completely trust the developers not to stop the Dapp or modify it in a way that benefits themselves. If a contract has no controller, then the Dapp cannot be upgraded at all and security vulnerabilities cannot be fixed, making data in it unsafe.

But don't worry, there is a third option - handing over control of the Dapp to a DAO, so the community can collectively decide the direction of the Dapp through an open governance mechanism. A DAO Canister controls the Dapp. This both protects users, since control is no longer in the hands of a few, and allows users to join governance and directly influence the Dapp's development.



### SNS

What's unique about ICP is that it can host fully on-chain Dapps (frontend, backend logic and data). Therefore the SNS DAO can fully control (via voting) all aspects of the Dapp, since everything is on-chain. Having a fully on-chain DAO is important because it allows all decisions to be executed on-chain. This contrasts with other blockchain DAOs, where voting happens on-chain but execution of outcomes is often done off-chain by developers.

ICP provides a DAO solution for Dapps to use called the Service Nervous System (SNS). During SNS creation, new tokens are minted and sold in a community-based fundraising. Control of the Dapp is transferred to the SNS, and anyone holding SNS tokens can contribute to decisions about the Dapp's future development.

Commemorative website for the launch of the first SNS project on IC: [https://sqbzf-5aaaa-aaaam-aavya-cai.ic0.app](https://sqbzf-5aaaa-aaaam-aavya-cai.ic0.app/)



### DAO and Open Source Projects

**"Imagine building a Twitter-like application where the logic and data are not controlled by a centralized company, but by the smart contracts of a DAO!"**

By adopting a DAO, control of these applications can be handed over to the token holders, the users. Whoever holds tokens has decision power. The community can collectively decide the direction of the application through voting, like which new features should be developed first, which are not needed, etc.

When developers update the code, they need to upload the latest code and submit a proposal containing the code hash, so anyone can view and audit the code. If there are no issues with the code, and the updated features are what the community wants, the proposal will likely pass. After the proposal passes the Canister will automatically update.

This is hugely important for users. The application is no longer at the mercy of a centralized dev team, real control is returned to users' hands. Except for the community all agreeing to take the application down, users can ensure the developers cannot unilaterally stop service, remove features or push malicious code updates. For developers, the DAO is also a good thing. It can attract more like-minded collaborators and bring more resources to the project. Developers no longer worry about users leaving, because the users are the owners of the project.

The DAO can also enable the application to be tokenized, issuing tokens for fundraising. Users can invest in the project by purchasing tokens, and the more valuable the tokens become, the more incentive users have to support the project. Moreover, code must be openly auditable by the community before deployment, which implicitly furthers the development of open source projects. Open source projects can raise funds through DAOs!

DAO enables true decentralized governance and empowerment for blockchain applications. It gives users, rather than developers, ultimate control. It can be said that DAO represents the best practice of the blockchain ethos, representing a future where blockchain technology empowers users and communities.

<br>

More on SNS:

SNS FAQ answering various questions: https://internetcomputer.org/sns/faq

How to participate in SNS: https://wiki.internetcomputer.org/wiki/SNS_decentralization_swap_trust

SNS docs: https://internetcomputer.org/docs/current/developer-docs/integrations/sns