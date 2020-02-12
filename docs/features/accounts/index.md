title: Overview

This section is an overview of **Accounts** on Algorand. It reviews core terminology and guides developers on how to interpret these terms in different contexts. 


# Terminology
## Keys and Addresses

Algorand keys use Ed25519 high-speed, high-security elliptic-curve signatures. They are produced through standard, open-source cryptographic libraries packaged with each of the SDKs. The creation algorithm takes a random value as input and outputs two 32-byte arrays, representing a public key and its associated private key. These are also referred to as a public/private key pair. These keys perform important cryptographic functions like signing data and verifying signatures. 

<center> ![Key Generation](../../imgs/accounts-0.png) </center>
<center>*Public/Private Key Generation* </center>

For reasons that include the need to make the keys human-readable and robust to human error when transferred, both the public and private keys undergo transformations. The output of these transformations is what the majority of developers, and usually all end-users, see. In fact, the Algorand developer tools actively seek to mask the complexity involved in these transformations. So unless you are a protocol-level developer modifying cryptographic-related source code, you may never actually encounter the true public/private key pair. 


### Transformation: Public Key to Algorand Address

The **public key** is transformed into a public Algorand address, by adding a 4-byte checksum to the end of the public key and then encoding it in base32. The result is what both the developer and end-user recognize as an **Algorand address**. The address is 58 characters long.

<center> ![Algorand Address](../../imgs/accounts-1.png) </center>
<center>*Public Key to Algorand Address* </center>

!!! info
	Since users almost never see the true public key, and the Algorand address is a unique mapping back to the public key, the use of the term **public key** is frequently (and inaccurately) used to mean **address**. 

### Transformation: Private Key to base64 private key

A base64 encoded concatenation of the private and public keys is a representation of the private key most commonly used by developers interfacing with the SDKs. It is likely not a representation familiar to end users.

<center> ![base64 private key](../../imgs/accounts-2.png) </center>
<center>*Base64 Private Key* </center>

### Transformation: Private Key to 25-word mnemonic

The 25-word mnemonic is the most user-friendly representation of the private key. It is generated by converting the private key bytes into 11-bit integers and then mapping those integers to the [bip-0039 English word list](https://raw.githubusercontent.com/bitcoin/bips/master/bip-0039/english.txt), where integer _n_ maps to the word in the _nth_ position in the list. By itself, this creates a 24-word mnemonic. A checksum is added by taking the first two bytes of the hash of the private key and converting them to 11-bit integers and then to their corresponding word in the word list. This word is added to the end of the 24 words to create a 25-word mnemonic.

This representation is called the private key **mnemonic**. You may also see it referred to as a **passphrase**. 

<center> ![Mnemonic](../../imgs/accounts-3.png) </center>
<center>*Private Key Mnemonic* </center>

!!! info
	Both the base64 representation of a private key and the private key mnemonic are considered **private keys**. It is important to disambiguate in contexts where the representation is important. 

## Wallets

Wallets, in the context of Algorand developer tools, refer to wallets generated and managed by the Key Management Daemon (kmd) process. A wallet stores a collection of keys. kmd stores collections of wallets and allows users to perform operations using the keys stored within these wallets. Wallets are associated with a master key, represented as a 25-word mnemonic, from which all accounts in that wallet are derived. This allows the owner of the wallet to only need to remember a single passphrase for all of their accounts. Wallets are stored encrypted on disk. 

See [Wallet-derived (kmd)](./create.md#wallet-derived-kmd) accounts in the [Creation Methods](#creation-methods) section for more details.

## Accounts
Accounts are entities on the Algorand blockchain associated with specific onchain data, like a balance. An Algorand Address is the identifier for an Algorand account. 

After generating a private key and corresponding address, sending Algos to the address on Algorand will initialize its state on the Algorand blockchain and turn it into a true account. 

<center> ![Account](../../imgs/accounts-4.png) </center>
<center>*Initializing an Account* </center>

### Attributes
#### Minimum Balance
Every account on Algorand must have a minimum balance of 100,000 microAlgos. If ever a transaction is sent that would result in a balance lower than the minimum, the transaction will fail. The minimum balance increases with each asset holding the account has. Read more about assets and changes to the minimum balance requirement in the [Algorand Standard Assets](../asa.md) section.

#### Online/Offline
By default, Algorand accounts are set to **offline**. An **online** account is one that participates in Algorand consensus. For an account to go online, it must generate a participation key and send a special key registration transaction. Read more about how to register an account online in the [Network Participation](../../run-a-node/participate/index.md) section.

### Other Account Types
Creating an Algorand address from a public key, is not the only way. A valid address can also be produced from a compiled TEAL contract and through multisignature accounts. These accounts differ in how they authorize spends, but they look like any other account on Algorand. Read more about contract accounts in the [Algorand Smart Contracts](../asc1/modes.md) section. Multisignature accounts are described [below](./create.md#multisignature).

### Special Accounts

Two accounts carry special meaning on the Algorand blockchain. They are the **FeeSink** and the **RewardsPool**. The FeeSink is where all fees from transactions are sent. The FeeSink can only spend to the RewardsPool account. The RewardsPool holds the Algos that are distributed as rewards to Algorand accounts as defined by the protocol. 

_MainNet [FeeSink](../../reference/algorand-networks/mainnet.md#feesink-address) and [RewardsPool](../../reference/algorand-networks/mainnet.md#rewardspool-address) addresses_

_TestNet [FeeSink](../../reference/algorand-networks/testnet.md#feesink-address) and [RewardsPool](../../reference/algorand-networks/testnet.md#rewardspool-address) addresses_

## A note about term usage in these docs
Even in these docs, use of these terms may be inconsistent. At times this is a deliberate style choice to ensure clarity around a broader concept. Sometimes it is to emphasize the inherent pairing of the public and private portions of a key. (e.g. In code examples, it is sometimes clearer to name variables as such to emphasize the connection between these two entities). Other times it is to abstract away from complexity related to generating an account on Algorand. For example, code samples may use terms like "generateAccount" to generate a private key and Algorand address. There is an underlying assumption that this pair will be used as an Algorand account even though on generation it is not yet represented on the blockchain and therefore is not yet technically an Algorand account.


??? tip "Interpretation Guide"
	Here are a few examples of the way these terms appear in the wild and suggestions on how to interpret them. It is always best to ask for clarification when the terms are ambiguous.

	1. A developer says they are looking to create a wallet for Algorand and are asking about kmd. - _In this situation, it is probably best to direct them to this page so they understand all the ways to create accounts on Algorand. They may want to support more than one generation method, including kmd._
	2. A consumer says: "I have 5 accounts in my wallet." - _Consumers are likely referring to mobile or web wallets, and probably not the underlying kmd wallet mechanism._
	3. A protocol developer wants to explore the signature validation mechanism in the Algorand protocol. - _This developer will most definitely encounter the true public/private key pairs as well as many of the concepts detailed above._