# AKRE Token

## Network Primitives

### Epochs

Arkreen Network approximately every 60 minutes marks the passing of an epoch. Mining rewards are distributed per epoch (as opposed to per block in most blockchain-based systems). At the conclusion of each epoch, the Arkreen Foundation will update the ledger for all the AKRE produced in that epoch.

The epochs in different duration is listed in the below table: 
 
Duration | Epochs
---|---
Day | 24
Week | 168
Month | 720
Year | 8,640


### Transactions

The Arkreen Network has many transaction types. Following are the most important ones: Registration transaction: adding a Solar Miner to the Arkreen network; Rewards transaction: trigger the Arkreen network to reward each participant.

### Accounts, Wallets and Keys

The Arkreen Network uses an EVM compatible ERC20 smart contract to manage participant’s token balance. Users can use the Arkreen mobile application wallet and manage their balance.&#x20;

## What is AKRE?

AKRE (Arkreen Token) is the ERC20 cryptocurrency and protocol token for the Arkreen Network. Its symbol is AKRE. The Token can either be distributed as a reward to contributors who help build and maintain the Arkreen Network, and can also be used to pay Arkreen Network transaction fees and external entities payment for using the green energy data service provided by the Arkreen network. The maximum supply of AKRE is 10,000,000,000 (10 billion). All AKRE of the Arkreen network will be distributed to relevant contributors in batches according to the progress of network construction.

The final AKRE distribution is shown in the following table:

Allocatee | Allocation
---|---
Builders | 30%. Distributed over time, assigned to Arkreen miner owners including Gaming Miner (testnet only), Remote Miner and Standard Miner.
Core team | 20%. Distributed one time, assigned to core team members.
Investors | 20%. Distributed one time, assigned to the investors who will support Arkreen Network.
Treasury | 18%. Distributed one time.
Ecosystem | 10%. Distributed one time.
Advisors | 2%. Distributed one time.

The distribution of Builders rewards changes over time to align incentives with the needs of the network.

Allocatee | Emission Schedule
---|---
Builders | Reward tokens will be released in a periodic manner, each epoch, in accordance with the PoGG work of the builders.
Core team | Token will be locked for one year, then fully vested daily for 4 years.
Investors | The vesting plan for investors will be set into different tiers.
Treasury | Token will be vested to the treasury when token generated.
Ecosystem | Token will be vested to the treasury for allocation when token generated.
Advisors | Token will be vested to the treasury for allocation when token generated.

## Proof of Green-Energy Generation

The Arkreen Network uses a novel algorithm called **Proof of Green-Energy Generation** (PoGG) to monitor Arkreen Network’s green power generation. All miners who correctly fulfill the PoGG task will be rewarded. The PoGG helps the Arkreen Network measure its generation capacity, which is the basis for the network to provide green energy data asset related services.

### How does a Miner fulfill the PoGG task?

The Arkreen network rewards Miners who consistently report green electricity production data with high quality. All Miners connected to the Arkreen network need to sample data every 5 minutes and cache it locally, and report the data cached in the current time period every 1 hour. After Miner reports data, it can clear the local cache data and prepare for the next data report. The data that Miner needs to sample mainly include:

Field | Unit | Description
---|---|---
timestamp | second | Sample time, seconds since 1970/01/01 00:00:00
power | mW (milliWatt) | Real-time power generation at sampling
total | mWh (milliwatt*hour) | Miner's cumulative energy generation since onboarding


### How does Arkreen Network determine which Miner will be rewarded?

Every miner has a chance to earn the PoGG reward about every 60 epochs. In the first block of every epoch, Arkreen Network will generate a key pair, and publish the public key (verifiable random number). In the first block of the next epoch, Arkreen Network will use the private key of the key pair to decide the miners that will be rewarded and publish the private key (verifiable evidence). The decision process as the following flow:

+ Each reward period (epoch) starts, assuming the block height is H, Arkreen Network randomly generates key pairs SK and PK, the public key PK is submitted to the network as the commitment transaction content, the private key SK is kept by the network.
+ When the block height is H+60, the reward will be distributed. Assume that the number of legal data sent by Miner in the statistical period is N, and assuming that Miner is eligible for 1 reward roughly every K blocks, the initial state K=60.
+ Calculate a SEED for each Miner, the calculation method is: SEED = SHA256(ECDH(SK, PKMiner) * N, BlockHashH..., BlockHashH+59) % K, PKMiner is the public key corresponding to the miner address
+ When the SEED = 0 and the miner has legal upload data in the current epoch, then the miner can participate in reward distribution.
+ In the reward distribution transaction, the network needs to disclose the private key SK
, any miner can later judge whether the reward distribution is fair or not based on the private key SK disclosed by the network. In any epoch, if no miner is rewarded, the token of the reward pool will be given to Arkreen Treasury.

### How does Arkreen Network calculate the token amount that a Miner will earn?

Arkreen Network distributes the reward to the miners according to the trustworthiness of green energy generation data, the normalized quantity of data and the normalized volume of energy, the detailed calculation process is as follows:

+ According to verifiable evidence, filter the list of all miners eligible for rewards.
+ Calculate the data authenticity of all miners eligible for rewards in the current epoch, the quantity of normalized reported data, and the normalized energy volume.
+ Calculate the contribution of each miner as: authenticity * quantity * volume
+ Calculate the proportion of rewards that each miner should receive, which is the proportion of each miner's total contribution to all winning miners.
+ Proportionately distributed to each winning miner based on the amount of AKRE minted per epoch.

### How does Arkreen Network verify the trustworthiness of PoGG data?

In order to avoid miners reporting false power generation data to the network, Arkreen Network uses a novel method for data authenticity judgment. The judging method is as follows: firstly,  according to the external weather and photovoltaic oracle data, it is judged whether the power generation data is within the legal range. If the data exceeds the upper limit of the theoretical power generation capacity, the data trustworthiness score will be 0, and the miners will not be able to obtain reward income. Secondly, according to the average real-time power of the miner, the real-time power data obtained by sampling the data statistics window is normalized, and the real-time power vector is obtained. Thirdly, find other miners within a certain distance around the miner, and calculate the similarity between the target miner and the real-time power vector of each surrounding miner. The more similarities between the surrounding miners and the target miner's real-time power vector, the more rewards the target miner gets, but the total number does not exceed 6.

### How does Arkreen Network calculate a miner’s normalized* data quantity?

In order to encourage miners to report power generation data stably for a long time, and avoid miners reporting too much data to harm the normal operation of Arkreen Network, Arkreen Network uses the following regularization calculation method to count the amount of power generation data reported by miners.

The statistical time window for the network to calculate the amount of data sent by each miner is 168 epochs (about 1 week). In each epoch time period, a miner can report data multiple times, but the number of legal data recognized in one epoch is 12 (sampling once every 5 minutes, each epoch is 60 minutes, so the number of legal sampling data is 12 ). Therefore, the amount of data reported by the miner is the total amount of data reported by all epochs within the statistical time window. If the miner does not report data within an epoch, the number of data in the epoch is 0.

### How does Arkreen Network calculate a miner’s normalized energy volume?

Because the power generation of large power plants is much larger than that of small power plants, in order to avoid most of the rewards being allocated to large power plants and the enthusiasm of small power plants to participate, Arkreen Network regularizes the power generation of miners. The calculation method is: The power generation of each miner in the statistical time window is calculated in mWh units, and the power generation data is raised to the fifth power, which is the normalized power generation data.
