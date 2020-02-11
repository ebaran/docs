title: Transaction Fields

Each table below specifies the **field name**, whether it is optional or required, its **type** within the protocol code (note that SDKs input types for these fields may differ), the **codec**, which is the name of the field when viewed within a transaction, and a **description** of the field. 

# Common Fields (`Header` and `Type`)
These fields are common to all transactions.


|Field|Required|Type|codec| Description|
|---|---|---|---|---|
|<a name="sender">Sender</a>| _required_ |Address|`"snd"`|The address of the account that pays the fee and amount.|
|<a name="fee">Fee</a>| _required_| uint64|`"fee"`|Paid by the sender to the FeeSink to prevent denial-of-service. The minimum fee on Algorand is currently 1000 microAlgos.|
|<a name="firstvalid">FirstValid</a>| _required_ | uint64 | `"fv"`|The first round for when the transaction is valid. If the transaction is sent prior to this round it will be rejected by the network.|
|<a name="lastvalid">LastValid</a>| _required_ | uint64 | `"lv"`|The ending round for which the transaction is valid. After this round, the transaction will be rejected by the network.|
|<a name="note">Note</a>|_optional_|[]byte|`"note"`| Any data up to 1000 bytes. |
|<a name="genesisid">GenesisID</a>|_optional_|string|`"gen"`| The human-readable string that identifies the network for the transaction. The genesis ID is found in the genesis block. See the genesis ID for [MainNet](../reference-docs/algorand-networks/mainnet.md#genesis-id), [TestNet](../reference-docs/algorand-networks/testnet.md#genesis_id), and [BetaNet](../reference-docs/algorand-networks/betanet.md#genesis-id). |
|<a name="genesishash">GenesisHash</a>|_required_|[32]byte|`"gh"`|The hash of the genesis block of the network for which the transaction is valid. See the genesis hash for [MainNet](../reference-docs/algorand-networks/mainnet.md#genesis-hash), [TestNet](../reference-docs/algorand-networks/testnet.md#genesis-hash), and [BetaNet](../reference-docs/algorand-networks/betanet.md#genesis-hash).
<a name="group">Group</a>|_optional_|[32]byte|`"grp"`|The group specifies that the transaction is part of a group and if so specifies the hash of the transaction group. Assign a group ID to a transaction through the workflow described in the [Atomic Transfers Guide](../feature-guides/atomic_transfers.md).|
<a name="lease">Lease</a>|_optional_|[32]byte|`"lx"`|A lease enforces mutual exclusion of transactions. If this field is nonzero, then once the transaction is confirmed, it acquires the lease identified by the (Sender, Lease) pair of the transaction until the LastValid round passes. While this transaction possesses the lease, no other transaction specifying this lease can be confirmed.  A lease is often used in the context of Algorand Smart Contracts to prevent replay attacks. Read more about [Algorand Smart Contracts](../feature-guides/asc1/index.md) and see the Delegate [Key Registration TEAL template](../reference-docs/teal/templates/delegate_keyreg.md) for an example implementation of leases. Leases can also be used to safeguard against unintended duplicate spends. For example, if I send a transaction to the network and later realize my fee was too low, I could send another transaction with a higher fee, but the same lease value. This would ensure that only one of those transactions ends up getting confirmed during the validity period. |
|<a name="type">TxType</a>|_required_|string|`"type"`| Specifies the type of transaction. This value is automatically generated using any of the developer tools.

# Payment Transaction
Transaction Object Type: `PaymentTx`

Includes all fields in [Header](#common-fields-header-and-type) and `"type"` is `"pay"`.

|Field|Required|Type|codec| Description|
|---|---|---|---|---|
|<a name="receiver">Receiver</a>| _required_ |Address|`"rcv"`|The address of the account that receives the [amount](#amount).|
|<a name="amount">Amount</a>|_required_|uint64|`"amt"`| The total amount to be sent in microAlgos.|
|<a name="closeremainderto">CloseRemainderTo</a>|_optional_|Address|`"close"`|When set, it indicates that the transaction is requesting that the [Sender](#sender) account should be closed, and all remaining funds, after the [fee](#fee) and [amount](#amount) are paid, be transferred to this address.|

# Key Registration Transaction
Transaction Object Type: `KeyRegistrationTx`

Includes all fields in [Header](#common-fields-header-and-type) and `"type"` is `"keyreg"`.

|Field|Required|Type|codec| Description|
|---|---|---|---|---|
|<a name="votepk">VotePk</a>| _required for online_ |ed25519PublicKey|`"votekey"`|The root participation public key. See [Generate a Participation Key](../network-participation/participate-in-consensus/generate_keys.md) to learn more.|
|<a name="selectionpk">SelectionPK</a>|_required for online_|VrfPubkey|`"selkey"`| The VRF public key.|
|<a name="votefirst">VoteFirst</a>|_required for online_|uint64|`"votefst"`|The first round that the *participation key* is valid. Not to be confused with the [FirstValid](#firstvalid) round of the keyreg transaction.|
|<a name="votelast">VoteLast</a>|_required for online_|uint64|`"votelst"`|The last round that the *participation key* is valid. Not to be confused with the [LastValid](#lastvalid) round of the keyreg transaction.|
|<a name="votekeydilution">VoteKeyDilution</a>|_required for online_|uint64|`"votekd"`|This is the dilution for the 2-level participation key. |
|<a name="nonparticipation">Nonparticipation</a>|_optional_|bool|`"nonpart"`| All new Algorand accounts are participating by default. This means that they earn rewards. Mark an account nonparticipating by setting this value to `true` and this account will no longer earn rewards. It is unlikely that you will ever need to do this and exists mainly for economic-related functions on the network.|

# Asset Configuration Transaction
Transaction Object Type: `AssetConfigTx`

Includes all fields in [Header](#common-fields-header-and-type) and `"type"` is `"acfg"`.

|Field|Required|Type|codec| Description|
|---|---|---|---|---|
|<a name="configasset">ConfigAsset</a>| _required, except on create_ |uint64|`"caid"`|For re-configure or destroy transactions, this is the unique asset ID. On asset creation, the ID is set ot zero.|
|[AssetParams](#asset-parameters) |_required, except on destroy_|[AssetParams](#asset-parameters)|`"apar"`| See AssetParams table for all available fields.|

## Asset Parameters
Object Name: (`AssetParams`)

|Field|Required|Type|codec| Description|
|---|---|---|---|---|
|<a name="creator">Creator</a>| _required on creation_ |Address|`"creator"`|The address for the account that creates the asset. This is the address where the parameters for this asset can be found, and also the address where unwanted asset units can be sent to be destroyed.|
|<a name="total">Total</a>|_required on creation_|uint64|`"total"`| The total number of base units of the asset to create. This number cannot be changed.|
|<a name="decimals">Decimals</a>|_required on creation_|uint32|`"decimals"`| The number of digits to use after the decimal point when displaying the asset. If 0, the asset is not divisible. If 1, the base unit of the asset is in tenths. If 2, the base unit of the asset is in hundredths. |
|<a name="defaultfrozen">DefaultFrozen</a>|_required on creation_|bool|`"defaultfrozen"`| True to freeze holdings for this asset by default. |
|<a name="unitname">UnitName</a>|_optional_|string|`"unitname"`| The name of a unit of this asset. Supplied on creation. Examples: USDT |
|<a name="assetname">AssetName</a>|_optional_|string|`"assetname"`| The name of the asset. Supplied on creation. Examples: Tether|
|<a name="url">URL</a>|_optional_|string|`"url"`| Specifies a URL where more information about the asset can be retrieved. Max size is 32 bytes. |
|<a name="metadatahash">MetaDataHash</a>|_optional_|[]byte|`"metadatahash"`| This field is intended to be a 32-byte hash of some metadata that is relevant to your asset and/or asset holders. The format of this metadata is up to the application. This field can _only_ be specified upon creation. An example might be the hash of some certificate that acknowledges the digitized asset as the official representation of a particular real-world asset.  |
|<a name="manageraddr">ManagerAddr</a>|_optional_|Address|`"managerkey"`| The address of the account who can manage the configuration of the asset and destroy it. |
|<a name="reserveaddr">ReserveAddr</a>|_optional_|Address|`"reserveaddr"`| The address of the account that holds the reserve (non-minted) units of the asset. This address has no specific authority in the protocol itself. It is used in the case where you want to signal to holders of your asset that the non-minted units of the asset reside in an account that is different from the default [creator](#creator) account. |
|<a name="freezeaddr">FreezeAddr</a>|_optional_|Address|`"freezeaddr"`| The address of the account used to freeze holdings of this asset. If empty, freezing is not permitted. |
|<a name="clawbackaddr">ManagerAddr</a>|_optional_|Address|`"clawbackaddr"`| The address of the account who can clawback holdings of  this asset. If empty, clawback is not permitted. |

# Asset Transfer Transaction 
Transaction Object Type: `AssetTransferTx`

Includes all fields in [Header](#common-fields-header-and-type) and `"type"` is `"axfer"`.

|Field|Required|Type|codec| Description|
|---|---|---|---|---|
|<a name="xferasset">XferAsset</a>| _required_ |uint64|`"xaid"`|The unique ID of the asset to be transferred.|
|<a name="assetamount">AssetAmount</a>|_required_|uint64|`"aamt"`| The amount of the asset to be transferred. A zero amount transferred to self allcoates that asset in the account's Asset map.|
|<a name="assetsender">AssetSender</a>|_required_|Address|`"asnd"`|The sender of the transfer. The regular [sender](#sender) field should be used and this one set to the zero value for regular transfers between accounts. If this value is non-zero, it indicates a clawback transaction where the [sender](#sender) is the asset's clawback address and the asset sender is the address from which the funds will be withdrawn.|
|<a name="assetreceiver">AssetReceiver</a>|_required_|Address|`"arcv"`| The recipient of the asset transfer.|
|<a name="assetcloseto">AssetCloseTo</a>|_required_|Address|`"aclose"`|Specify this field to remove the asset holding from the [sender](#sender) account and reduce the account's minimum balance. 

# Asset Freeze Transaction
Transaction Object Type: `AssetFreezeTx`

Includes all fields in [Header](#common-fields-header-and-type) and `"type"` is `"afrz"`.

|Field|Required|Type|codec| Description|
|---|---|---|---|---|
|<a name="freezeaccount">FreezeAccount</a>| _required_ |Address|`"fadd"`|The address of the accoutn whose asset is being frozen or un-frozen.|
|<a name="freezeasset">FreezeAsset</a>|_required_|uint64|`"faid"`| The asset ID being frozen or unfrozen.|
|<a name="assetfrozen">AssetFrozen</a>|_required_|bool|`"afrz"`| True to freeze the asset.|

# Signed Transaction
Transaction Object Type: `SignedTxn`

|Field|Required|Type|codec| Description|
|---|---|---|---|---|
|<a name="sig">Sig</a>| _required, if no other sig specified_ |crypto.Signature|`"sig"`|TO DO|
|<a name="msig">Msig</a>|_required, if no other sig specified_|crypto.MultisigSig|`"msig"`| TO DO|
|<a name="lsig">LogicSig</a>|_required, if no other sig specified_|LogicSig|`"lsig"`| TO DO|
|<a name="txn">Transaction</a>|_required_|Transaction|`"txn"`| [`PaymentTx`](#payment-transaction), [`KeyRegistrationTx`](#key-registration-transaction), [`AssetConfigTx`](#asset-configuration-transaction), [`AssetTransferTx`](#asset-transfer-transaction), or [`AssetFreezeTx`](#asset-freeze-transaction)

