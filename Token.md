# Token
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  constructor(uint _initialSupply) public {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public view returns (uint balance) {
    return balances[_owner];
  }
}
```

# Solution
This will take advatage of integer overflow. We ill call the transfer() function and specifically focusing on:
> require(balances[msg.sender] - _value >= 0);

Solidity itself does not do too well with integer overlfow and underflow so we will take advatage of this by giving this a very large number to manipulate. 

We will need a wallet that is different from the wallet we are signing the transaction with. We can use the following where xyz = wallet address:
await contract.transfer('xyz', '100000000000000000000000000000000000000000000000000');

# What I learned
1. Solidity does not inherently protect against integer overflow/underflow.
2. Make sure to do multiple checks before transfering and updating balances.
