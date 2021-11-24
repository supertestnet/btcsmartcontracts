# General

**Smart contract**

A smart contract is a bitcoin address which, if you send money to it, has this property: only you can recover the money in that address, with an important exception. The exception is: if someone executes a script chosen by you, the script executor may take a fee (set by you). Also, the script may specify one or more addresses where some or all of the money in the smart contract should go after the script is executed. Alternatively, if the script doesn't specify such an address, any remaining money stays in the smart contract address until it's all consumed by fees or until you recover whatever is left.

# Basic tools

**Hashlock**

A lock which prevents a user from withdrawing funds from an address unless they know a password called a hashlock key

**Timelock**

An address from which funds can be withdrawn only after some number of seconds or blocks

**Multisig**

An address from which funds can be withdrawn only if some threshold of keyholders agree

**Data address**

A bitcoin address which does not have a private key but solely consists of 32 bytes of arbitrary data chosen by the address creator. A user who sends sats to X data addresses can add X*32 bytes of arbitrary data to bitcoin’s blockchain and to its chainstate. Data addresses have been used to add hundreds of kilobytes of arbitrary data to bitcoin’s blockchain in a single transaction. This is widely regarded as a dumb move and is referred to by many as chain spam.

**Op_return**

A function which can be added to a bitcoin transaction to burn an arbitrary number of the sender’s sats forever and simultaneously add up to 80 bytes of arbitrary data to bitcoin’s blockchain but not to its chainstate

**Presigned withdrawal**

A transaction which can withdraw funds from one predetermined address -- Address A -- to another predetermined address -- Address B -- if and only if the right amount of funds arrives in Address A

# Intermediate tools

**Channel**

A 2 of 2 multisig with 2 keyholders

**Federation**

An M of N multisig with N keyholders where M is a number lower than N

**PSBT**

A transaction creation standard allowing for one or more parties to cosign a transaction if and only if the signer or signers approve of where it will send their money and what fee will be taken

**Modern colored coins**

Tokens other than bitcoin which are issued and transferred by means of messages added to bitcoin’s blockchain via data addresses or op_returns

**Classic colored coins**

Actual sats which are made uniquely identifiable among other sats (i.e. “colored”) through messages added to bitcoin’s blockchain via data addresses or op_returns. The color of one or more sats may be transmitted with those sats by ensuring that every transfer of those sats is done in such a way that the data which colors the sats can still be associated with the sats after they move. Coloring is removable by mixing one or more colored sats with regular sats in such a way that it is not clear which of the post-mixed sats are colored anymore.

# Advanced tools

**Oracle**

A service which triggers a bitcoin payment based on the outcome of a real world event

**Hashlock oracle**

A type of oracle which uses a channel with two hashlock spending conditions to create a scenario where the oracle can trigger a bitcoin payment from one party to the other and thus resolve a smart contract without the oracle ever taking custody of either party’s funds. The spending conditions specify that one party can withdraw the funds using one hashlock key or the other party can withdraw the funds using a different hashlock key. The hashlock keys are both known only to the oracle. After funds are deposited to the channel by one or both parties, the oracle assesses the outcome of a real world event and then publishes the hashlock key needed by one of the parties to withdraw all the funds. The other party gets nothing. Hashlock oracles are useful in scenarios where the oracle doesn’t need to know who the counterparties are. An anonymous hashlock oracle can announce on its website that on some future day it will publish a hashlock key for a future event and that key will either be valid for hashlock X (if the event goes one way) or for hashlock Y (if the event goes the other way). Users can then -- without ever speaking to the oracle -- create contracts where they can only withdraw funds if they, in the future, learn the one key or the other. When the day comes that the oracle publishes the “winning” key on its website, the contract “winner” can get that key from the oracle’s website and use it to withdraw the funds from their contract -- without ever interacting with the oracle directly.

**DLC oracle**

Similar to the above except instead of relying on hashlocks and waiting for the oracle to reveal a hashlock key, the parties to a contract sign a transaction sending money to the other party, encrypt the signature using an encryption key provided by the oracle on his or her website, and then send the encrypted signature to their counterparty along with a proof that the signature, if decrypted, is a valid signature which will allow the recipient to withdraw the funds in the contract. The oracle then reveals a decryption key instead of a hashlock key after the real world event occurs. DLC oracles are even more private than hashlock oracles because the use of a hashlock oracle involves broadcasting data to the blockchain that could alert the oracle to the fact that two parties are planning to use the oracle’s data in their contract. With dlcs, the users never need to do anything that could alert the oracle that his data is being used by someone.

**Escrow oracle**

A type of oracle which uses a 2 of 3 federation to create a scenario where the oracle can trigger a bitcoin payment from one party to the other and resolve a smart contract without the oracle ever taking custody of either party’s funds. After funds are deposited to the multisig by one or both counterparties, the oracle sends his key to one of the other two parties depending on the outcome of a real world event. That party then has the exclusive ability to withdraw all of the funds in the contract to any address they want. An escrow oracle is the simplest, oldest, and most widely used type of oracle in bitcoin.

**HTLC (hashed timelocked contract)**

A channel where one keyholder can immediately withdraw funds using a hashlock and the other keyholder can withdraw funds without a hashlock but only after a timelock expires. Useful in cases where the hashlock is withheld from the party who can use it unless certain conditions are fulfilled before the timelock expires.

**Atomic swap**

A procedure by which one or more parties can swap bitcoins for colored coins or altcoins without any party having the ability to rugpull any other party. This is accomplished using two HTLCs where the hashlock, timelock, and keyholders are identical. The differences between the HTLCs are: bitcoins are deposited to one while colored coins or altcoins are deposited to the other, each keyholder plays the opposite role in each HTLC, and only one keyholder knows the hashlock key. To accomplish an atomic swap, the colored coin or altcoin holder deposits his or her colored coins or altcoins to the htlc where the bitcoin holder can use the hashlock to withdraw them, and the bitcoin holder deposits his or her bitcoins to the htlc where the colored coin or altcoin holder can use the hashlock to withdraw them. Once the deposits are made, the party who knows the hashlock key can use it to withdraw the other party’s coins, but doing so reveals the hashlock key to the other party by broadcasting it onto the blockchain as part of the withdrawal transaction. That party can then also use it to withdraw the first party’s coins. Thus the two parties can swap bitcoins and colored coins or altcoins without either party having the power to rugpull the other.

**HTLC oracle**

A type of oracle which uses a set of HTLCs to create a scenario where the oracle can trigger a bitcoin payment from one party to the other and thus resolve a smart contract without the oracle ever taking custody of either party’s funds. One or both counterparties deposit funds to an HTLC where the oracle can withdraw the funds if it learns the hashlock key and where the hashlock key is only known to that HTLC’s depositor’s counterparty. The result is one or two funded HTLCs. Next, the oracle assesses the outcome of a real world event, and in regard to it the oracle selects one of the counterparties as the “winner.” If the winner is supposed to receive funds (such as in a betting contract where both sides bet on an outcome), the oracle prepares an additional HTLC with the winner. This second HTLC uses a hashlock identical to the one whose hashlock key is known by the winner. The oracle deposits the amount the winner has “won” into this second HTLC. This money can be withdrawn by the winner if and only if he or she broadcasts a transaction to the blockchain revealing the hashlock key to that contract. If he or she does so, the oracle now has the hashlock key it needs to withdraw the funds that come from the other party -- the “loser” -- thus reimbursing the oracle with the loser’s money. If the “winner” originally deposited funds into an htlc, the timelock on that HTLC will eventually expire, and at that point the winner can recover the funds he or she deposited into it. The loser gets nothing and cannot recover any funds because that money went into the oracle’s pocket when the oracle reimbursed itself. An HTLC oracle is the most complex type of oracle in bitcoin but also happens to be the easiest kind of oracle to implement on lightning.

**Multisig covenant**

Either a channel or a federation where M is the same thing as N. An essential feature of a multisig covenant is that one or more users must have the ability to make a presigned withdrawal from the multisig. The threshold number of keyholders try to agree at some point in the future on how to spend funds from the multisig but if they can’t agree in the future, a user with the presigned withdrawal can use it to send the funds in the multisig to an address that the keyholders agreed on in the past.

**Hebrew hodl**

A multisig covenant where the presigned withdrawal, if used, burns all the funds in the multisig. The name is inspired by a story in the bible where Abraham and one of his servants, during a difficult situation, grabbed each other by the balls and swore an oath to help one another. (Genesis 24:1-9) The gesture is widely interpreted as signifying that both men knew the only way they could get out of their situation unharmed was by cooperating. In bitcoin, a hebrew hodl can be used by two parties to put an equal amount of each party’s funds at risk of being lost forever unless both parties agree on how to spend the money, such as to split it half and half after each party plays his part in some contract.

# Coordination tools

**Offer**

A standard for expressing intent to be a counterparty in a smart contract

**Orderbook**

An array of one or more offers. Accepting an offer initiates a connection between two or more counterparties through which they can exchange data for purposes of fulfilling the offer via a smart contract

**Tcp service**

A self-hosted program for posting, reading, and fulfilling offers involving messages that are sent over the regular internet

**Tor service**

A self-hosted program for posting, reading, and fulfilling offers involving messages that are sent over the onion network

**Irc channel**

A chat server hosted on third party servers where users can post, read, and fulfill offers

**Website**

A set of one or more html files hosted by a third party where users can post, read, and fulfill offers

# Examples of best practices

**Lightning**

A set of programs where users create (and occasionally deposit funds into) htlcs that are themselves created within a network of channels. Coordination happens via tcp services. The hashlock keys to funded htlcs are exchanged by participants to simulate the transmission of money.

**Bisq**

A program where users create multisig covenants within channels. Coordination happens via tor services. Both parties to a covenant deposit funds to the multisig as collateral and one party -- the bitcoin seller -- also deposits additional sats in an amount equal to however much he or she is selling. Their counterparty then sends them colored coins, altcoins, or sometimes real dollars. If both parties are satisfied with the trade, they cosign a transaction to disburse their collateral back to each other and send the additional bitcoins to the bitcoin buyer. If, instead, either party is not satisfied with the trade, that party can use a presigned withdrawal to send all the funds in the multisig (including the collateral) to bisq’s donation address. Bisq’s volunteers may then investigate the transaction, decide if they think one or both parties deserves reimbursement, and then act accordingly. If one of bisq’s volunteers reimburses anyone, they are themselves expected to get reimbursed later via the money in bisq’s donation fund.

**Hodlhodl loans**

Hodlhodl is a company that runs an escrow oracle service and uses it to facilitate non-custodial loans. Hodlhodl’s users can create loan offers or borrow offers and coordinate on hodlhodl’s website. After a borrower deposits funds to a multisig, these funds are treated as collateral, and the lender is expected to send stablecoins to the borrower. Hodlhodl then sends their key to the borrower if two conditions are true: first, bitcoin’s price remains above a liquidation threshhold, and second, the principal of the loan plus interest (denominated in stablecoins) is sent to the lender’s address before a pre-agreed time. If either of those two conditions is false, hodlhodl sends their key to the lender who can then withdraw the collateral to reimburse himself or herself for the loss of his or her stablecoins.

**Hodlhodl trades**

Hodlhodl trades are similar to hodlhodl loans except the bitcoin seller is the one who deposits funds to the multisig. Hodlhodl sends their key to the buyer if a pre-agreed amount of stablecoins (or sometimes real dollars) are sent to the seller’s address before a pre-agreed time. Otherwise, they send their key to the seller who can then withdraw the funds so that he or she loses nothing except two transaction fees.

**Hodlhodl predictions**

Hodlhodl predictions are similar to hodlhodl loans except both parties deposit funds to the multisig. Hodlhodl sends their key to whichever party correctly predicted a future event. That party can then withdraw the funds as their rightful winnings.

**Joinmarket**

Joinmarket is a program for interactively signing PSBTs for use in coinjoins. Some participants (called “makers”) create coinjoin offers whereby they agree to do the coinjoin if and only if other participants (called “takers”) pay them a fee. Coordination happens via IRC channels.

**Opendex**

A program for swapping bitcoins for altcoins. Users create swap offers whereby they agree to exchange bitcoins or altcoins for other bitcoins or altcoins if and only if their counterparty pays them a fee. Coordination happens on opendex’s website.

**Omni and counterparty**

Two different colored coin protocols where token issuance and transference terms are encoded in bitcoin’s blockchain via op_return. Omni wallets and counterparty wallets know how to interpret these terms and use them to infer the current owner of a given colored coin. The omni and counterparty protocols don’t count as best practices because they are widely criticized and rarely used due to the way in which they (expensively) add lots of data to bitcoin’s blockchain which could be avoided if the terms of issuances and transfers were stored somewhere other than on bitcoin’s blockchain.
