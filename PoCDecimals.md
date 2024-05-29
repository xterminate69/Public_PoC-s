1. Add this simpe getter function to the contract LockManager
```solidity

    function testconfguredToken() public returns(bool)
    {
      address configuretoken = address(0x2B5AD5c4795c026514f8317c7a215E218DcCD6cF);
      if(configuredTokens[configuretoken].active) return true;
      return false;
    }
```
2.Add this test 

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Test, console} from "forge-std/Test.sol";
import {LockManager} from "2024-05-munchables/managers/LockManager.sol";
import "2024-05-munchables/interfaces/ILockManager.sol";
import {ConfigStorage} from "2024-05-munchables/config/ConfigStorage.sol";
import {AccessManager} from "@openzeppelin/contracts/access/manager/AccessManager.sol";
contract CounterTest is Test 
{
  ConfigStorage stor;
  LockManager lock;
  function setUp() public
  { 
    stor = new ConfigStorage();
    address addressofConfigStorage = address(stor);
    lock = new LockManager(addressofConfigStorage);
  }
  function testwhitelisttoken() public
  {
    ILockManager.ConfiguredToken memory CT;
    CT.usdPrice = 4;
    CT.nftCost = 5;
    CT.decimals = 19;
    CT.active = true; // this is set to true becuase the LockManager does never set it to true
    address addresstoconfigure = vm.addr(2);
    lock.configureToken(addresstoconfigure, CT);
    bool return1 = lock.testconfguredToken();
    require(return1 == true);
  }
}
```
For this PoC to work you need to remove `onlyAdmin` in `configureToken`.

The respective results 
```
  [123329] CounterTest::testwhitelisttoken()
    ├─ [0] VM::addr(<pk>) [staticcall]
    │   └─ ← [Return] 0x2B5AD5c4795c026514f8317c7a215E218DcCD6cF
    ├─ [114023] LockManager::configureToken(0x2B5AD5c4795c026514f8317c7a215E218DcCD6cF, ConfiguredToken({ usdPrice: 4, nftCost: 5, decimals: 19, active: true }))
    │   ├─ emit TokenConfigured(_tokenContract: 0x2B5AD5c4795c026514f8317c7a215E218DcCD6cF, _tokenData: ConfiguredToken({ usdPrice: 4, nftCost: 5, decimals: 19, active: true }))
    │   └─ ← [Stop] 
    ├─ [435] LockManager::testconfguredToken()
    │   └─ ← [Return] true
    └─ ← [Stop] 

Suite result: ok. 1 passed; 0 failed; 0 skipped; finished in 4.28ms (553.45µs CPU time)

Ran 1 test suite in 6.15s (4.28ms CPU time): 1 tests passed, 0 failed, 0 skipped (1 total tests)


```

