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
