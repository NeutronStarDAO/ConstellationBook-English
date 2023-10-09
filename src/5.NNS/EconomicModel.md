## Introduction to the ICP Economic Model

The ICP token economy includes aspects like token supply, distribution, use cases, and mechanisms to maintain network stability and encourage DAO participation.

Through a well-designed tokenomics model, IC aims to create a sustainable ecosystem that encourages participation, growth, and trust in the network by developers, DAO participants, node providers, and Dapp users.



### Design Intent

The ICP tokenomics was designed to balance the following goals and constraints:

* Developers pay extremely low and stable costs for storage and computation.
* Node provider rewards need to be high enough to incentivize them to join and serve the network.
* Governance voting rewards need to strike a balance - encourage users to stake ICP and participate in governance while keeping inflation in check.
* This is a dynamic system. The community needs to closely monitor system conditions and adjust various parameters of the ICP economy via NNS proposals for continual evolution and optimization.



### Use Cases of ICP

To understand IC's economic model, one must first understand the various use cases of ICP. As the native utility token of the ICP blockchain, ICP has the following primary uses:

1. Rewards for node providers

    Node providers supplying computing and storage infrastructure for the ICP blockchain can earn ICP rewards. Each node receives a fixed amount every month, computed in fiat terms but paid in ICP. This model ensures node provider costs remain stable in fiat terms. Rewards vary slightly to encourage geographic distribution of nodes globally. As long as they provide quality service, node providers can continually earn these rewards. Rewards are paid through minting new ICP, leading to inflationary pressure.

2. Staked in the NNS

    ICP holders can stake (lock up) their tokens to spawn governance neurons. Holding neurons gives them voting power via IC's chain-based open governance system NNS. The NNS allows neuron holders who staked ICP to vote on proposals to upgrade blockchain protocol, node software, accept new node providers, etc. The NNS implements liquid democracy - neurons can choose to follow other influential neurons. Neuron holders participating in governance voting earn ICP rewards from the blockchain protocol. Rewards are earned in maturity, convertible to ICP. This conversion is regulated by the NNS to control inflation.

3. Fuel for computation

    Computation and storage resources consumed by canister smart contracts running on the ICP blockchain need to be paid for by burning ICP-generated "Cycles". These Cycles are somewhat analogous to "gas" in Ethereum. However, ICP uses a "contract pays" model where contracts pre-load themselves with Cycles versus users paying for contract usage. Cycles are produced by burning ICP, causing deflationary pressure.

4. Fees for transactions and proposals

    When users transfer ICP between wallets on the IC network, a small portion of ICP is paid as the on-chain transaction fee. Users also pay an ICP fee when submitting proposals to the governance system. These fees are acquired through burning ICP, producing deflation.



### Total Token Supply

The total supply of ICP is not fixed - it fluctuates based on preset inflation and deflation mechanisms.

At mainnet launch on May 10, 2021, the total supply was 469 million ICP with 123 million ICP in circulation.

Currently (as of Sep 14, 2023), the total supply is 505.8 million ICP with 444.6 million ICP in circulation (87.5% of total supply).

* Staked ICP is 251.8 million ICP (49.8% of total supply)
    * 81.8% of staked ICP chose unlocking delays longer than 1 year.
    * 52.8% of staked ICP chose an 8 year unlocking delay.

> Circulation refers to ICP that has ever circulated. Part of it is locked in neurons as staked ICP.



### ICP Minting and Burning

The NNS mints new ICP through:

* Staking ICP to participate in network governance.
* Monthly rewards for node providers.

The NNS burns ICP through:

* Burning ICP to mint Cycles to pay for computation and storage costs of smart contracts.
* ICP transaction fees. Every ICP transfer on IC incurs a fixed 0.0001 ICP fee.
* After rejecting an NNS proposal, the ICP submitted with the proposal is burned.

Current circulating ICP and historical amounts minted and burned can be seen here. There is also a post on Dfinity research estimates for total ICP supply, if interested.



### Governance Economics

For governance, preventing any single party from acquiring 51% of voting power is critical. Even influencing vote outcomes without reaching 51% would be detrimental. Amassing such vast amounts of ICP would require huge capital, and these locked assets would also devalue if the network is attacked. So such an attack would be hugely unprofitable. Even malicious actors with deep pockets would find it hard to quickly acquire so much ICP from exchanges, since most are locked earning yields in neurons. This would force them to gradually accumulate over longer timespans, but the purchasing pressure itself would drive up prices, making subsequent purchases harder and harder.

Achieving 51% voting power without obtaining locked ICP is highly unrealistic. This is why the ICP network disallows neuron trading on markets. Because in times of market panic, malicious actors could compel holders to dump neurons cheaply and swiftly aggregate voting power. Of course, pulling off such an attack would be extremely difficult, but it does reflect the importance of prohibiting free neuron trading.