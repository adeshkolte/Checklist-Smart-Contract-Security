# Checklist-Smart-Contract-Security


#Correct use of function visibility modifiers

Internal functions are marked as such and only the proper author can call the function.

Please see The Parity Wallet Hack Explained.
Call stack attack

Synonyms: Shallow stack attack, stack attack

Prerequisites: Functions uses send() or call()

Invoking: The attacker manipulates cross-contract call stack to call() to fail by calling contract with stack of 1023.

Protection: Always check return value of a send() and call(). Prefer someAddress.send() over someAddress.call.value()

More info

    http://martin.swende.se/blog/Devcon1-and-contract-security.html

#Re-entrancy attack

Synonyms: Race condition

Prerequisites: Functions uses send() or call() for ethers, or transferFrom() for ERC-20 tokens or send() for ERC-777 tokens.

Invoking: The untrusted called contract calls the same function back, having it in unexpected state. This is how TheDAO was hacked.The attack can be chained over several of functions (cross function race condition).

Protection: Protect your functions with re-entrancy guards. Use Check-Effect-Interact order of actions in your functions that call anything that could be reflected back to the smartcontract.
Check-effects-interact

Use this pattern to minimize the damage of potential re-entry attack.

    First Check, run things like require()

    Then Effect, update counters, like balance[address] -= 10

    Last, do anything that is Interact and will run code in other contracts through send(), call(), transferFrom() and others.

More info

    https://github.com/ConsenSys/smart-contract-best-practices

    https://twitter.com/bneiluj/status/1251448415503908864

    Does reentrancy attack happens as soon as the balance in storage is modified after the withdrawal?

#DoS with unexpectd throw

Prerequisites: Functions uses send() or call() with throw following on fail

Invoking: The attacker manipulates the contract state so that send() always fails (e.g. refund)

Protection: Prefer pull payment system over send()

More info

    https://github.com/ConsenSys/smart-contract-best-practices

#Economic attacks

Prerequisies: Your smart contract reads price data and trades based on it

Invoking: Arbitration opportunites and unsafe price feeds may not be exploits per se, but still expose the users to the loss of funds that could have been otherwise avoided. In an economic attack, the attacker exploits the opportunity to trade for profit against someone, usually based on disparencies in the price of a token.

Protection: Do not rely on the spot price given by the exchanges or automated market-making smart contracts. All prices, including stablecoin prices, are subject to manipulation.

Popular automated market makers and smart contracts provide safe functions to calculate the price in different situations.

More info

    https://twitter.com/bantg/status/1320765422581538817

#Malicious libraries

Prerequisites: Using an external contract as a library and obtaining it through the registry.

Invoking: Call another contract function through a contract registry (see library keyword in Solidity).

Protection: Ensure no dynamic parts which can be swapped out in future versions.

    http://martin.swende.se/blog/Devcon1-and-contract-security.html

#Integer overflow

Prerequisites: Function accepts an uint argument with is used in math

Invoking: Sending very big or very negative integer causing the sum calculation to overflow

Protection: Always check the order of values when doing math operations. E.g. https://github.com/Firstbloodio/token/blob/master/smart_contract/FirstBloodToken.sol

More info

    Is it possible to overflow uints?

#Integer division round down

Prerequisites: Payment logic requires division operator /

Invoking: Programmer's error

Protection: Be aware that divisions are always rounded down
Loop length and gas manipulation

Others: Allocating too small int for arrays

Prerequisites: Any loop, copy arrays or strings inside the storage. A for loop where contract users can increase the length of the loop. Consider voting scenario loops.

Invoking: The attacker increases the array length or manipulates block gas limit

Protection: Use pull style payment systems. Spread send() over multiple transactions and check msg.gas limit.

    https://blog.ethereum.org/2016/06/10/smart-contract-security/

    https://github.com/ConsenSys/smart-contract-best-practices

    https://ethereum.stackexchange.com/a/7298/620

#Fallback function consuming more than the limit of 2300 gas

Prerequisites: A Solidity contract with catch all function() { } to receive generic sends

Invoking: Programmer's error

Protection: 100% test coverage. Make sure your fallback function stays below 2300 gas. Check for all branches of the function using test suite. Don't store anything in fallback function. Don't call contracts or send ethers in fallback function.

More info:

    https://blog.ethereum.org/2016/06/10/smart-contract-security/

    https://github.com/ConsenSys/smart-contract-best-practices

#Forced balance update

Prerequisites: Function reads contract total balance and has some logic depending on it

Invoking: selfdestruct(contractaddress) can forcible upgrade its balance

Protection: Don't trust this.balance to stay within given limits

More

    https://github.com/ConsenSys/smart-contract-best-practices

#Miner frontrunning

Synonym: Transaction-Ordering Dependence (TOD)

Prerequisites: A bid style market, like DAI liqudation and auctions

Invoking: The attacker sees transactions in a mempool before they are finalized in blockchain. The attacker has a priviledged connection, like a mining pool, to broadcast his transaction first and override the original benefactor.

Protection: Pre-commit schemes

More

    https://www.theblockcrypto.com/post/45750/exploring-defi-trading-strategies-arbitrage-in-defi

    https://github.com/ConsenSys/smart-contract-best-practices

#Static analysis tools

Static analysis tools that check the code for commonly known errors, like integer oveflows. They cannot check the intent of the code, but they run try to analyse the code against well known common problems.

    Myhtril

    Slither

Resources

    OpenZeppelin checklist for things to do before a smart contract audit

    https://github.com/ConsenSys/smart-contract-best-practices

    https://blog.ethereum.org/2016/06/10/smart-contract-security/
