# IOTA-Commit-Reveal
Python Implementation of the Commit Reveal Scheme Using IOTA 


## What is Commit Reveal?
Commit-Reveal is a Cryptographic primitive which allows user Alice to proof knowledge/possession of a statement "S" at time "T" without giving away any information about statement "S". 
Commit-Reveal is done in two Steps: 
- the commit phase at time "T" in which Alice commits an signs the encrypted statement 
- the reveal phase at time "T+1" in which Alice reveals the statement and the encryption-key

Alice can now proof she had knowledge/possession of S at time T by using the revealed key to generate the same hash as initially commited 

## How is it Implemented?
To implement Commit-Reveal using IOTA we need to make 2 0-value transactions.

First we need to make the Commit Transaction, here the hashed message+key is broadcatsed to the network, where it is immutably stored. After some time, once we are ready to reveal the secret we make the Reveal Transaction to the same address. This time the message and key are not hashed. To check the proof we go to the original Commit Transaction and compare it to the hash of the message and key of the Reveal transaction. If the match, bothe message and key need to have been known at the time the Commit Transaction was done. 

Given that IOTA works with Ternary both the message and the key need to first be encoded into a string of Trytes. 

## Encoding and Commit generation
The Commit transaction is generated by taking a string of charachter length lower than 49 (99 Trytes). Transforming it into a TrytesString which will be called TryteStatement. The charachter length of the resulting TrytesString is also taken and encoded into a TryteString of 4 charachters(as this number is <99), and is called TryteStatementLength. Than a random 9 charachter TryteString Salt is generated. To create the commit message the following concatenations are done:
 ```
 Message = TryteStatementLength + "9" + TryteStatement + "9" + Salt
 ```
This message is stored and will be reveled in the Reveal Transaction.
To create the Commit transaction this message String is hashed using SHA256, the resulting 64 bytes string is converted into a 128 TrytesString and used in the Commit Transaction.

## Checking Commit and decoding Reveal
To Check the validity of the Commit Transaction we take the message TryteString from the Reveal Transaction, hash it using SHA256, transform the bytes string into TrytesString and compare it to the message of the Commit Transaction.

To decode the the message of the Reveal Transaction we take the first 4 charachters of the TrytesString, decode them from TrytesString to string and get the number of charachters composing the TrytesStatement, we skip the 9 used as separation and take the given number of charachters from the TrytesString, we can now decode the statemen by converting the TrytesString back to a string.

## Why use IOTA
The Tangle gives the ability to store small amounts of data on an immutable ledger for free. This means that once the Commit transaction is attached there is no way for Alice to change its content at a later stage, making the commit binding.
Even though other DLTs can do the same, the ability to make the commits for free makes IOTA the best choiche for this.

## What to do with Commit-Reveal?
Commit-Reveal is a primitive used in more complex Schemes such as Zero-Knowledge Proofs, whitch makes it verry usefull aready.
It can also be used for any application which requires a user to commit to a statement without revealing it. For example if Alice and Bob want to make a bet on the dollar price of IOTA on a given date, but Alice does not want Bob to get influenced by her bet, she migth commit the price she bets on with Commit-Reveal and reveal it only after the bet expired. Bob can not know what price Alice bid on until she reveals it, but Bob can verify that the reveled price is acctualy the one she initialiy commited.
