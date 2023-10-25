# Privacy

Nothing in the ethereum blockchain is private. The keyword private is merely an artificial construct of the Solidity language. Web3's `getStorageAt(...)` can be used to read anything from storage. It can be tricky to read what you want though, since several optimization rules and techniques are used to compact the storage as much as possible.

It can't get much more complicated than what was exposed in this level. For more, check out this excellent article by "Darius": [How to read Ethereum contract storage](https://medium.com/aigang-network/how-to-read-ethereum-contract-storage-44252c8af925)

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Privacy {

  bool public locked = true;
  uint256 public ID = block.timestamp;
  uint8 private flattening = 10;
  uint8 private denomination = 255;
  uint16 private awkwardness = uint16(block.timestamp);
  bytes32[3] private data;

  constructor(bytes32[3] memory _data) {
    data = _data;
  }
  
  function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }

  /*
    A bunch of super advanced solidity algorithms...

      ,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`
      .,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,
      *.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^         ,---/V\
      `*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.    ~|__(o.o)
      ^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'  UU  UU
  */
}
```

# Solution

I used ethers.js for this to read the data storage of the contract. it provided 3 different bytes32 data and I turned each into 16 and tried each for the unlock. The position was actually [5] instead of [2] or [3] like directions lead you to believe.

ALWAYS VERIFY!

```
// using ethers v6

import { BrowserProvider } from "ethers";
import './App.css'
import { useEffect } from "react";

function App() {
  const { ethereum } = window;
  const contractAddress = '0x899eD01Db91DEb22ADD6FC3Db3F98d4998667e3d';
  // const slot = 1;

  const connectToWallet = async () => {
    if(ethereum) {
      try {

        const provider = new BrowserProvider(ethereum);
        console.log('Provider: ', provider)

        const signer = await provider.getSigner();
        console.log('Signer: ', signer)

        for(let i = 0; i <= 10; i++) {
          const data = await provider.getStorage(contractAddress, i);
          console.log('Data: ', data);
        }

      } catch(err) {
        console.log('Error: ', err);
      }
    }
  }

  useEffect(() => {
    connectToWallet();
  }, []);

  return (
    <>
      <h1>Solving Privacy Challenge</h1>
    </>
  )
}

export default App

```

# What I learned

1. Everything on the blockchain is readable, even private variables within the contract. These variables may not be able to be used, but can be viewed.
2. You can use front-end frameworks like ethers.js to get desired results and not just Remix.
