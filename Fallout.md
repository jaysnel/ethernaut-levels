# Fallout

### Contract Code
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import 'openzeppelin-contracts-06/math/SafeMath.sol';

contract Fallout {
  
  using SafeMath for uint256;
  mapping (address => uint) allocations;
  address payable public owner;


  /* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }

  modifier onlyOwner {
	        require(
	            msg.sender == owner,
	            "caller is not the owner"
	        );
	        _;
	    }

  function allocate() public payable {
    allocations[msg.sender] = allocations[msg.sender].add(msg.value);
  }

  function sendAllocation(address payable allocator) public {
    require(allocations[allocator] > 0);
    allocator.transfer(allocations[allocator]);
  }

  function collectAllocations() public onlyOwner {
    msg.sender.transfer(address(this).balance);
  }

  function allocatorBalance(address allocator) public view returns (uint) {
    return allocations[allocator];
  }
}
```
# Solution
This contract takes advantage of a very simple mistake which is not requiring condtions in order to claim ownership of the contract.
This contract has many functions but what we want to pay attention to is Fal1out(). This function allows whovever runs it to claim ownership of the contract.

Run:
> await contract.Fal1out()

After running, we will be the new owner. We can verify this by running:
> await contract.owner

Now we need to drain the contract of its funds. There is a function called collectAllocations() that allows the owner to transfer funds to thier adderess.

Run:
> await contract.collectAllocations()

# What I learned
1. Make sure that when changing ownership of a contract that you have specifc requirements for such action or to now allow a change of ownership at all.
