Is coin age reset by another incoming transaction?
==================================================
http://www.reddit.com/r/peercoin/comments/1wny1x/two_questions_about_minting/
1. I have an address that has held 1000 PPC from one input for 30 days. I now receive a second input of 10 PPC into that address. Does (a) the minting age for the entire adress (1010 PPC) now reset to zero, or (b) does the original (1000 PPC) input within that address keep its age and only the new input (10 PPC) within that address gets a zero age?
2. I have ten computers, each running a copy of the client. Each client's wallet contains that same (1000 PPC) address. Are my minting chances ten times better than if I had run just one client?

1. (b): When you [look for a Proof of Stake](https://github.com/ppcoin/ppcoin/blob/master/src/wallet.cpp#L1213) you [select all your coins (minus reserve)](https://github.com/ppcoin/ppcoin/blob/master/src/wallet.cpp#L1240) that [you have not spent](https://github.com/ppcoin/ppcoin/blob/master/src/wallet.cpp#L951). Then you try to find one of these transactions (an amount of available coins is in reality just the output of an unspent transaction) that [is a valid PoS](https://github.com/ppcoin/ppcoin/blob/master/src/wallet.cpp#L1268) at current time. [This check](https://github.com/ppcoin/ppcoin/blob/master/src/kernel.cpp#L271) will verify the coin age (so your 10 PPC transaction will fail) and generate [a hash only involving the transaction, its block and the current time](https://github.com/ppcoin/ppcoin/blob/master/src/kernel.cpp#L251). So your 1000 PPC transaction can still succeed if the generated hash matches the difficulty.
2. No. To generate a PoS block you must find a kernel (an unspent received transaction) and a timestamp (the current time in seconds) that (when hashed) match the current difficulty. Both are the same on all your computers so you don't increase your chances of finding a good hash (you'll get the same hash everywhere). The official client already tries this [every 0.5 seconds](https://github.com/ppcoin/ppcoin/blob/master/src/main.cpp#L3939). That was recently discussed [here](http://www.peercointalk.org/index.php?topic=1956.msg17752#msg17752).


How is the reserve balance chosen?
==================================
http://www.peercointalk.org/index.php?topic=2196.0


What stops me from POS minting several different chains at once?
================================================================
http://www.peercointalk.org/index.php?topic=1956.0


Can we have paper wallet PoS?
=============================

TL;DR: Yes but we must change the protocol.

I think Sunny King is already planning a solution but I don't know if he mentioned how or when.

For the network to be secure, you need to prove you own aged coins to generate a PoS block (otherwise anyone could easily destroy the network by generating as many blocks as they want). Right now the only way to prove that you own them is by signing a transaction with the private key of your coins.

But the bitcoin system is very flexible so we can quite easily change the protocol to allow special transaction (that was done to allow multisig for example).

Most of the transactions are simple "pay to address". That means they have a script attached that says "you can do whatever you want with these coins if you can sign with the private key of address X."
On multisig transactions, the script says (for example) "you can do whatever you want if you can sign with 2 private keys of addresses X, Y and Z."

In our case we could allow transactions with a script that says "you can generate a PoS block if you can sign with the private key of address X, and whatever you want if you can sign with the private key of address Y."
This way you could have a private key that can only be used as PoS. You could keep it unlocked without too much risk.

We would have to force the output to be the same as the input in this PoS block (which is not the case right now: you can change your client to make it move your coin when it finds a PoS block).

I don't know if there already are such transactions in bitcoin where a signature is only valid in a specific context. I think it should work. It'd probably require a new script "word" (something like "are we doing PoS?").

It would also help on the issue mentioned above where you don't know which coins are really used for minting.

Building a good user interface for that may take some time.

I think that's one possible solution. There are certainly others.

Anyway I think we can't implement a solution only on the client side because when you received your coin it was explicitly said that only your private key can use them and the protocol obeys that even during PoS. We need to change the protocol (i.e. all clients).

http://www.peercointalk.org/index.php?topic=2196.0


Grinding attack
===============

guest2324q:

The bitcoin wiki have been updated. Yesterday Peercoin was pointed out as not having a decentralized solution for the stake-grinding issue: https://en.bitcoin.it/w/index.php?title=Altcoin&action=historysubmit&diff=48013&oldid=47950

would you disagree?

search this document for grind http://download.wpsoftware.net/bitcoin/pos.pdf

sigmike:

this grinding attack doesn't work on peercoin because the block hash is not used in the proof of stake process

so you can't try many block hash to find one that make you find the next block

it's true that there are centralized checkpoints though

but they will probably be removed (or made disabled by default) in the next version

so conclusion in this text is misleading

the centralized checkpoints exist, but not because of grinding attacks

they exist partially because of the nothing at stake problem described above

and they will be removed probably because the cost is becoming too high as more people mint
