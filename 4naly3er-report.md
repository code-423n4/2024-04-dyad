# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings) | 5 |
| [GAS-2](#GAS-2) | For Operations that will not overflow, you could use unchecked | 36 |
| [GAS-3](#GAS-3) | Avoid contract existence checks by using low level calls | 2 |
| [GAS-4](#GAS-4) | State variables only set in the constructor should be declared `immutable` | 8 |
| [GAS-5](#GAS-5) | Functions guaranteed to revert when called by normal users can be marked `payable` | 4 |
| [GAS-6](#GAS-6) | `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`) | 4 |
| [GAS-7](#GAS-7) | Using `private` rather than `public` for constants, saves gas | 5 |
| [GAS-8](#GAS-8) | Use shift right/left instead of division/multiplication if possible | 1 |
| [GAS-9](#GAS-9) | Increments/decrements can be unchecked in for-loops | 4 |
| [GAS-10](#GAS-10) | Use != 0 instead of > 0 for unsigned integer comparison | 2 |
### <a name="GAS-1"></a>[GAS-1] `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings)
This saves **16 gas per instance.**

*Instances (5)*:
```solidity
File: /src/core/Vault.kerosine.sol

37:         id2asset[id] += amount;

43:         id2asset[to] += amount;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

52:             tvl +=

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

210:             totalUsdValue += usdValue;

224:             totalUsdValue += usdValue;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="GAS-2"></a>[GAS-2] For Operations that will not overflow, you could use unchecked

*Instances (36)*:
```solidity
File: /script/deploy/Deploy.V2.s.sol

37:         vm.startBroadcast(); // ----------------------

100:         vm.stopBroadcast(); // ----------------------------

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/script/deploy/Deploy.V2.s.sol)

```solidity
File: /src/core/Vault.kerosine.bounded.sol

38:         return unboundedKerosineVault.assetPrice() * 2;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/Vault.kerosine.sol

37:         id2asset[id] += amount;

42:         id2asset[from] -= amount;

43:         id2asset[to] += amount;

48:         return (id2asset[id] * assetPrice()) / 1e8;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

35:         id2asset[id] -= amount;

50:         for (uint i = 0; i < numberOfVaults; i++) {

52:             tvl +=

53:                 (vault.asset().balanceOf(address(vault)) *

54:                     vault.assetPrice() *

55:                     1e18) /

56:                 (10 ** vault.asset().decimals()) /

57:                 (10 ** vault.oracle().decimals());

59:         uint numerator = tvl - dyad.totalSupply();

61:         return (numerator * 1e8) / denominator;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

25:     uint public constant MIN_COLLATERIZATION_RATIO = 1.5e18; // 150%

26:     uint public constant LIQUIDATION_REWARD = 0.2e18; //  20%

116:         uint value = (amount * _vault.assetPrice() * 1e18) /

117:             10 ** _vault.oracle().decimals() /

118:             10 ** _vault.asset().decimals();

119:         if (getNonKeroseneValue(id) - value < dyadMinted)

131:         uint newDyadMinted = dyad.mintedDyad(address(this), id) + amount;

154:         uint asset = (amount *

155:             (10 ** (_vault.oracle().decimals() + _vault.asset().decimals()))) /

156:             _vault.assetPrice() /

173:         uint liquidationEquityShare = (cappedCr - 1e18).mulWadDown(

176:         uint liquidationAssetShare = (liquidationEquityShare + 1e18).divWadDown(

181:         for (uint i = 0; i < numberOfVaults; i++) {

198:         return getNonKeroseneValue(id) + getKeroseneValue(id);

204:         for (uint i = 0; i < numberOfVaults; i++) {

210:             totalUsdValue += usdValue;

218:         for (uint i = 0; i < numberOfVaults; i++) {

224:             totalUsdValue += usdValue;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

```solidity
File: /src/staking/KerosineDenominator.sol

18:         return kerosine.totalSupply() - kerosine.balanceOf(MAINNET_OWNER);

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/staking/KerosineDenominator.sol)

### <a name="GAS-3"></a>[GAS-3] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*Instances (2)*:
```solidity
File: /src/core/Vault.kerosine.unbounded.sol

53:                 (vault.asset().balanceOf(address(vault)) *

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/staking/KerosineDenominator.sol

18:         return kerosine.totalSupply() - kerosine.balanceOf(MAINNET_OWNER);

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/staking/KerosineDenominator.sol)

### <a name="GAS-4"></a>[GAS-4] State variables only set in the constructor should be declared `immutable`
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around **20 000 gas** per variable) and replace the expensive storage-reading operations (around **2100 gas** per reading) to a less expensive value reading (**3 gas**)

*Instances (8)*:
```solidity
File: /src/core/Vault.kerosine.sol

31:         vaultManager = _vaultManager;

32:         asset = _asset;

33:         kerosineManager = _kerosineManager;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

27:         dyad = _dyad;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

53:         dNft = _dNft;

54:         dyad = _dyad;

55:         vaultLicenser = _licenser;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

```solidity
File: /src/staking/KerosineDenominator.sol

11:         kerosine = _kerosine;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/staking/KerosineDenominator.sol)

### <a name="GAS-5"></a>[GAS-5] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (4)*:
```solidity
File: /src/core/KerosineManager.sol

18:     function add(address vault) external onlyOwner {

23:     function remove(address vault) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.sol

36:     function deposit(uint id, uint amount) public onlyVaultManager {

41:     function move(uint from, uint to, uint amount) external onlyVaultManager {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

### <a name="GAS-6"></a>[GAS-6] `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)
Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name *post-increment*:

```solidity
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
  
However, pre-increments (or pre-decrements) return the new value:
  
```solidity
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```

In the pre-increment case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`.

Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).

*Saves 5 gas per instance*

*Instances (4)*:
```solidity
File: /src/core/Vault.kerosine.unbounded.sol

50:         for (uint i = 0; i < numberOfVaults; i++) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

181:         for (uint i = 0; i < numberOfVaults; i++) {

204:         for (uint i = 0; i < numberOfVaults; i++) {

218:         for (uint i = 0; i < numberOfVaults; i++) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="GAS-7"></a>[GAS-7] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (5)*:
```solidity
File: /src/core/KerosineManager.sol

14:     uint public constant MAX_VAULTS = 10;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/VaultManagerV2.sol

22:     uint public constant MAX_VAULTS = 5;

23:     uint public constant MAX_VAULTS_KEROSENE = 5;

25:     uint public constant MIN_COLLATERIZATION_RATIO = 1.5e18; // 150%

26:     uint public constant LIQUIDATION_REWARD = 0.2e18; //  20%

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="GAS-8"></a>[GAS-8] Use shift right/left instead of division/multiplication if possible
While the `DIV` / `MUL` opcode uses 5 gas, the `SHR` / `SHL` opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version `0.8+`
- Use `>> 1` instead of `/ 2`
- Use `>> 2` instead of `/ 4`
- Use `<< 3` instead of `* 8`
- ...
- Use `>> 5` instead of `/ 2^5 == / 32`
- Use `<< 6` instead of `* 2^6 == * 64`

TL;DR:
- Shifting left by N is like multiplying by 2^N (Each bits to the left is an increased power of 2)
- Shifting right by N is like dividing by 2^N (Each bits to the right is a decreased power of 2)

*Saves around 2 gas + 20 for unchecked per instance*

*Instances (1)*:
```solidity
File: /src/core/Vault.kerosine.bounded.sol

38:         return unboundedKerosineVault.assetPrice() * 2;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

### <a name="GAS-9"></a>[GAS-9] Increments/decrements can be unchecked in for-loops
In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

[ethereum/solidity#10695](https://github.com/ethereum/solidity/issues/10695)

The change would be:

```diff
- for (uint256 i; i < numIterations; i++) {
+ for (uint256 i; i < numIterations;) {
 // ...  
+   unchecked { ++i; }
}  
```

These save around **25 gas saved** per instance.

The same can be applied with decrements (which should use `break` when `i == 0`).

The risk of overflow is non-existent for `uint256`.

*Instances (4)*:
```solidity
File: /src/core/Vault.kerosine.unbounded.sol

50:         for (uint i = 0; i < numberOfVaults; i++) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

181:         for (uint i = 0; i < numberOfVaults; i++) {

204:         for (uint i = 0; i < numberOfVaults; i++) {

218:         for (uint i = 0; i < numberOfVaults; i++) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="GAS-10"></a>[GAS-10] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (2)*:
```solidity
File: /src/core/VaultManagerV2.sol

82:         if (Vault(vault).id2asset(id) > 0) revert VaultHasAssets();

88:         if (Vault(vault).id2asset(id) > 0) revert VaultHasAssets();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | `constant`s should be defined rather than using magic numbers | 3 |
| [NC-2](#NC-2) | Control structures do not follow the Solidity Style Guide | 24 |
| [NC-3](#NC-3) | Function ordering does not follow the Solidity style guide | 2 |
| [NC-4](#NC-4) | Functions should not be longer than 50 lines | 22 |
| [NC-5](#NC-5) | Change uint to uint256 | 65 |
| [NC-6](#NC-6) | Lack of checks in setters | 3 |
| [NC-7](#NC-7) | Missing Event for critical parameters change | 3 |
| [NC-8](#NC-8) | NatSpec is completely non-existent on functions that should have them | 11 |
| [NC-9](#NC-9) | Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor | 2 |
| [NC-10](#NC-10) | Constant state variables defined more than once | 2 |
| [NC-11](#NC-11) | Consider using named mappings | 4 |
| [NC-12](#NC-12) | Take advantage of Custom Error's return value property | 23 |
| [NC-13](#NC-13) | Contract does not follow the Solidity style guide's suggested layout ordering | 2 |
| [NC-14](#NC-14) | Internal and private variables and functions names should begin with an underscore | 3 |
| [NC-15](#NC-15) | `public` functions not called by the contract should be declared `external` instead | 3 |
| [NC-16](#NC-16) | Variables need not be initialized to zero | 4 |
### <a name="NC-1"></a>[NC-1] `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*Instances (3)*:
```solidity
File: /src/core/Vault.kerosine.bounded.sol

38:         return unboundedKerosineVault.assetPrice() * 2;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

117:             10 ** _vault.oracle().decimals() /

118:             10 ** _vault.asset().decimals();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-2"></a>[NC-2] Control structures do not follow the Solidity Style Guide
See the [control structures](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures) section of the Solidity Style Guide

*Instances (24)*:
```solidity
File: /src/core/KerosineManager.sol

19:         if (vaults.length() >= MAX_VAULTS) revert TooManyVaults();

20:         if (!vaults.add(vault)) revert VaultAlreadyAdded();

24:         if (!vaults.remove(vault)) revert VaultNotFound();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.sol

22:         if (msg.sender != address(vaultManager)) revert NotVaultManager();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/VaultManagerV2.sol

40:         if (dNft.ownerOf(id) != msg.sender) revert NotOwner();

44:         if (dNft.ownerOf(id) == address(0)) revert InvalidDNft();

48:         if (!vaultLicenser.isLicensed(vault)) revert NotLicensed();

66:         if (vaults[id].length() >= MAX_VAULTS) revert TooManyVaults();

67:         if (!vaultLicenser.isLicensed(vault)) revert VaultNotLicensed();

68:         if (!vaults[id].add(vault)) revert VaultAlreadyAdded();

73:         if (vaultsKerosene[id].length() >= MAX_VAULTS_KEROSENE)

75:         if (!keroseneManager.isLicensed(vault)) revert VaultNotLicensed();

76:         if (!vaultsKerosene[id].add(vault)) revert VaultAlreadyAdded();

82:         if (Vault(vault).id2asset(id) > 0) revert VaultHasAssets();

83:         if (!vaults[id].remove(vault)) revert VaultNotAdded();

88:         if (Vault(vault).id2asset(id) > 0) revert VaultHasAssets();

89:         if (!vaultsKerosene[id].remove(vault)) revert VaultNotAdded();

112:         if (idToBlockOfLastDeposit[id] == block.number)

119:         if (getNonKeroseneValue(id) - value < dyadMinted)

122:         if (collatRatio(id) < MIN_COLLATERIZATION_RATIO) revert CrTooLow();

132:         if (getNonKeroseneValue(id) < newDyadMinted)

135:         if (collatRatio(id) < MIN_COLLATERIZATION_RATIO) revert CrTooLow();

169:         if (cr >= MIN_COLLATERIZATION_RATIO) revert CrTooHigh();

193:         if (_dyad == 0) return type(uint).max;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-3"></a>[NC-3] Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*Instances (2)*:
```solidity
File: /src/core/Vault.kerosine.sol

1: 
   Current order:
   public deposit
   external move
   public getUsdValue
   public assetPrice
   
   Suggested order:
   external move
   public deposit
   public getUsdValue
   public assetPrice

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/VaultManagerV2.sol

1: 
   Current order:
   external setKeroseneManager
   external add
   external addKerosene
   external remove
   external removeKerosene
   external deposit
   public withdraw
   external mintDyad
   external burnDyad
   external redeemDyad
   external liquidate
   public collatRatio
   public getTotalUsdValue
   public getNonKeroseneValue
   public getKeroseneValue
   external getVaults
   external hasVault
   
   Suggested order:
   external setKeroseneManager
   external add
   external addKerosene
   external remove
   external removeKerosene
   external deposit
   external mintDyad
   external burnDyad
   external redeemDyad
   external liquidate
   external getVaults
   external hasVault
   public withdraw
   public collatRatio
   public getTotalUsdValue
   public getNonKeroseneValue
   public getKeroseneValue

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-4"></a>[NC-4] Functions should not be longer than 50 lines
Overly complex code can make understanding functionality more difficult, try to further modularize your code to ensure readability 

*Instances (22)*:
```solidity
File: /script/deploy/Deploy.V2.s.sol

36:     function run() public returns (Contracts memory) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/script/deploy/Deploy.V2.s.sol)

```solidity
File: /src/core/KerosineManager.sol

23:     function remove(address vault) external onlyOwner {

27:     function getVaults() external view returns (address[] memory) {

31:     function isLicensed(address vault) external view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.bounded.sol

37:     function assetPrice() public view override returns (uint) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/Vault.kerosine.sol

36:     function deposit(uint id, uint amount) public onlyVaultManager {

41:     function move(uint from, uint to, uint amount) external onlyVaultManager {

47:     function getUsdValue(uint id) public view returns (uint) {

51:     function assetPrice() public view virtual returns (uint);

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

46:     function assetPrice() public view override returns (uint) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

65:     function add(uint id, address vault) external isDNftOwner(id) {

72:     function addKerosene(uint id, address vault) external isDNftOwner(id) {

81:     function remove(uint id, address vault) external isDNftOwner(id) {

87:     function removeKerosene(uint id, address vault) external isDNftOwner(id) {

140:     function burnDyad(uint id, uint amount) external isValidDNft(id) {

191:     function collatRatio(uint id) public view returns (uint) {

197:     function getTotalUsdValue(uint id) public view returns (uint) {

201:     function getNonKeroseneValue(uint id) public view returns (uint) {

215:     function getKeroseneValue(uint id) public view returns (uint) {

231:     function getVaults(uint id) external view returns (address[] memory) {

235:     function hasVault(uint id, address vault) external view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

```solidity
File: /src/staking/KerosineDenominator.sol

14:     function denominator() external view returns (uint) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/staking/KerosineDenominator.sol)

### <a name="NC-5"></a>[NC-5] Change uint to uint256
Throughout the code base, some variables are declared as `uint`. To favor explicitness, consider changing all instances of `uint` to `uint256`

*Instances (65)*:
```solidity
File: /src/core/KerosineManager.sol

14:     uint public constant MAX_VAULTS = 10;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.bounded.sol

13:     error NotWithdrawable(uint id, address to, uint amount);

30:         uint id,

32:         uint amount

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/Vault.kerosine.sol

19:     mapping(uint => uint) public id2asset;

36:     function deposit(uint id, uint amount) public onlyVaultManager {

41:     function move(uint from, uint to, uint amount) external onlyVaultManager {

47:     function getUsdValue(uint id) public view returns (uint) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

31:         uint id,

33:         uint amount

47:         uint tvl;

49:         uint numberOfVaults = vaults.length;

50:         for (uint i = 0; i < numberOfVaults; i++) {

59:         uint numerator = tvl - dyad.totalSupply();

60:         uint denominator = kerosineDenominator.denominator();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

22:     uint public constant MAX_VAULTS = 5;

23:     uint public constant MAX_VAULTS_KEROSENE = 5;

25:     uint public constant MIN_COLLATERIZATION_RATIO = 1.5e18; // 150%

26:     uint public constant LIQUIDATION_REWARD = 0.2e18; //  20%

34:     mapping(uint => EnumerableSet.AddressSet) internal vaults;

35:     mapping(uint => EnumerableSet.AddressSet) internal vaultsKerosene;

37:     mapping(uint => uint) public idToBlockOfLastDeposit;

39:     modifier isDNftOwner(uint id) {

43:     modifier isValidDNft(uint id) {

65:     function add(uint id, address vault) external isDNftOwner(id) {

72:     function addKerosene(uint id, address vault) external isDNftOwner(id) {

81:     function remove(uint id, address vault) external isDNftOwner(id) {

87:     function removeKerosene(uint id, address vault) external isDNftOwner(id) {

95:         uint id,

97:         uint amount

107:         uint id,

109:         uint amount,

114:         uint dyadMinted = dyad.mintedDyad(address(this), id);

116:         uint value = (amount * _vault.assetPrice() * 1e18) /

127:         uint id,

128:         uint amount,

131:         uint newDyadMinted = dyad.mintedDyad(address(this), id) + amount;

140:     function burnDyad(uint id, uint amount) external isValidDNft(id) {

147:         uint id,

149:         uint amount,

154:         uint asset = (amount *

165:         uint id,

166:         uint to

168:         uint cr = collatRatio(id);

172:         uint cappedCr = cr < 1e18 ? 1e18 : cr;

173:         uint liquidationEquityShare = (cappedCr - 1e18).mulWadDown(

176:         uint liquidationAssetShare = (liquidationEquityShare + 1e18).divWadDown(

180:         uint numberOfVaults = vaults[id].length();

181:         for (uint i = 0; i < numberOfVaults; i++) {

183:             uint collateral = vault.id2asset(id).mulWadUp(

191:     function collatRatio(uint id) public view returns (uint) {

192:         uint _dyad = dyad.mintedDyad(address(this), id);

197:     function getTotalUsdValue(uint id) public view returns (uint) {

201:     function getNonKeroseneValue(uint id) public view returns (uint) {

202:         uint totalUsdValue;

203:         uint numberOfVaults = vaults[id].length();

204:         for (uint i = 0; i < numberOfVaults; i++) {

206:             uint usdValue;

215:     function getKeroseneValue(uint id) public view returns (uint) {

216:         uint totalUsdValue;

217:         uint numberOfVaults = vaultsKerosene[id].length();

218:         for (uint i = 0; i < numberOfVaults; i++) {

220:             uint usdValue;

231:     function getVaults(uint id) external view returns (address[] memory) {

235:     function hasVault(uint id, address vault) external view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-6"></a>[NC-6] Lack of checks in setters
Be it sanity checks (like checks against `0`-values) or initial setting checks: it's best for Setter functions to have them

*Instances (3)*:
```solidity
File: /src/core/Vault.kerosine.bounded.sol

23:     function setUnboundedKerosineVault(
            UnboundedKerosineVault _unboundedKerosineVault
        ) external onlyOwner {
            unboundedKerosineVault = _unboundedKerosineVault;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

40:     function setDenominator(
            KerosineDenominator _kerosineDenominator
        ) external onlyOwner {
            kerosineDenominator = _kerosineDenominator;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

58:     function setKeroseneManager(
            KerosineManager _keroseneManager
        ) external initializer {
            keroseneManager = _keroseneManager;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-7"></a>[NC-7] Missing Event for critical parameters change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

*Instances (3)*:
```solidity
File: /src/core/Vault.kerosine.bounded.sol

23:     function setUnboundedKerosineVault(
            UnboundedKerosineVault _unboundedKerosineVault
        ) external onlyOwner {
            unboundedKerosineVault = _unboundedKerosineVault;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

40:     function setDenominator(
            KerosineDenominator _kerosineDenominator
        ) external onlyOwner {
            kerosineDenominator = _kerosineDenominator;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

58:     function setKeroseneManager(
            KerosineManager _keroseneManager
        ) external initializer {
            keroseneManager = _keroseneManager;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-8"></a>[NC-8] NatSpec is completely non-existent on functions that should have them
Public and external functions that aren't view or pure should have NatSpec comments

*Instances (11)*:
```solidity
File: /script/deploy/Deploy.V2.s.sol

36:     function run() public returns (Contracts memory) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/script/deploy/Deploy.V2.s.sol)

```solidity
File: /src/core/KerosineManager.sol

18:     function add(address vault) external onlyOwner {

23:     function remove(address vault) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.bounded.sol

23:     function setUnboundedKerosineVault(

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/Vault.kerosine.sol

36:     function deposit(uint id, uint amount) public onlyVaultManager {

41:     function move(uint from, uint to, uint amount) external onlyVaultManager {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

30:     function withdraw(

40:     function setDenominator(

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

58:     function setKeroseneManager(

72:     function addKerosene(uint id, address vault) external isDNftOwner(id) {

87:     function removeKerosene(uint id, address vault) external isDNftOwner(id) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-9"></a>[NC-9] Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor
If a function is supposed to be access-controlled, a `modifier` should be used instead of a `require/if` statement for more readability.

*Instances (2)*:
```solidity
File: /src/core/Vault.kerosine.sol

22:         if (msg.sender != address(vaultManager)) revert NotVaultManager();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/VaultManagerV2.sol

40:         if (dNft.ownerOf(id) != msg.sender) revert NotOwner();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-10"></a>[NC-10] Constant state variables defined more than once
Rather than redefining state variable constant, consider using a library to store all constants as this will prevent data redundancy

*Instances (2)*:
```solidity
File: /src/core/KerosineManager.sol

14:     uint public constant MAX_VAULTS = 10;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/VaultManagerV2.sol

22:     uint public constant MAX_VAULTS = 5;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-11"></a>[NC-11] Consider using named mappings
Consider moving to solidity version 0.8.18 or later, and using [named mappings](https://ethereum.stackexchange.com/questions/51629/how-to-name-the-arguments-in-mapping/145555#145555) to make it easier to understand the purpose of each mapping

*Instances (4)*:
```solidity
File: /src/core/Vault.kerosine.sol

19:     mapping(uint => uint) public id2asset;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/VaultManagerV2.sol

34:     mapping(uint => EnumerableSet.AddressSet) internal vaults;

35:     mapping(uint => EnumerableSet.AddressSet) internal vaultsKerosene;

37:     mapping(uint => uint) public idToBlockOfLastDeposit;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-12"></a>[NC-12] Take advantage of Custom Error's return value property
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

*Instances (23)*:
```solidity
File: /src/core/KerosineManager.sol

19:         if (vaults.length() >= MAX_VAULTS) revert TooManyVaults();

20:         if (!vaults.add(vault)) revert VaultAlreadyAdded();

24:         if (!vaults.remove(vault)) revert VaultNotFound();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.sol

22:         if (msg.sender != address(vaultManager)) revert NotVaultManager();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/VaultManagerV2.sol

40:         if (dNft.ownerOf(id) != msg.sender) revert NotOwner();

44:         if (dNft.ownerOf(id) == address(0)) revert InvalidDNft();

48:         if (!vaultLicenser.isLicensed(vault)) revert NotLicensed();

66:         if (vaults[id].length() >= MAX_VAULTS) revert TooManyVaults();

67:         if (!vaultLicenser.isLicensed(vault)) revert VaultNotLicensed();

68:         if (!vaults[id].add(vault)) revert VaultAlreadyAdded();

74:             revert TooManyVaults();

75:         if (!keroseneManager.isLicensed(vault)) revert VaultNotLicensed();

76:         if (!vaultsKerosene[id].add(vault)) revert VaultAlreadyAdded();

82:         if (Vault(vault).id2asset(id) > 0) revert VaultHasAssets();

83:         if (!vaults[id].remove(vault)) revert VaultNotAdded();

88:         if (Vault(vault).id2asset(id) > 0) revert VaultHasAssets();

89:         if (!vaultsKerosene[id].remove(vault)) revert VaultNotAdded();

113:             revert DepositedInSameBlock();

120:             revert NotEnoughExoCollat();

122:         if (collatRatio(id) < MIN_COLLATERIZATION_RATIO) revert CrTooLow();

133:             revert NotEnoughExoCollat();

135:         if (collatRatio(id) < MIN_COLLATERIZATION_RATIO) revert CrTooLow();

169:         if (cr >= MIN_COLLATERIZATION_RATIO) revert CrTooHigh();

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-13"></a>[NC-13] Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be:

1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

However, the contract(s) below do not follow this ordering

*Instances (2)*:
```solidity
File: /src/core/KerosineManager.sol

1: 
   Current order:
   ErrorDefinition.TooManyVaults
   ErrorDefinition.VaultAlreadyAdded
   ErrorDefinition.VaultNotFound
   UsingForDirective.EnumerableSet.AddressSet
   VariableDeclaration.MAX_VAULTS
   VariableDeclaration.vaults
   FunctionDefinition.add
   FunctionDefinition.remove
   FunctionDefinition.getVaults
   FunctionDefinition.isLicensed
   
   Suggested order:
   UsingForDirective.EnumerableSet.AddressSet
   VariableDeclaration.MAX_VAULTS
   VariableDeclaration.vaults
   ErrorDefinition.TooManyVaults
   ErrorDefinition.VaultAlreadyAdded
   ErrorDefinition.VaultNotFound
   FunctionDefinition.add
   FunctionDefinition.remove
   FunctionDefinition.getVaults
   FunctionDefinition.isLicensed

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.bounded.sol

1: 
   Current order:
   ErrorDefinition.NotWithdrawable
   VariableDeclaration.unboundedKerosineVault
   FunctionDefinition.constructor
   FunctionDefinition.setUnboundedKerosineVault
   FunctionDefinition.withdraw
   FunctionDefinition.assetPrice
   
   Suggested order:
   VariableDeclaration.unboundedKerosineVault
   ErrorDefinition.NotWithdrawable
   FunctionDefinition.constructor
   FunctionDefinition.setUnboundedKerosineVault
   FunctionDefinition.withdraw
   FunctionDefinition.assetPrice

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

### <a name="NC-14"></a>[NC-14] Internal and private variables and functions names should begin with an underscore
According to the Solidity Style Guide, Non-`external` variable and function names should begin with an [underscore](https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables)

*Instances (3)*:
```solidity
File: /src/core/KerosineManager.sol

16:     EnumerableSet.AddressSet private vaults;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/VaultManagerV2.sol

34:     mapping(uint => EnumerableSet.AddressSet) internal vaults;

35:     mapping(uint => EnumerableSet.AddressSet) internal vaultsKerosene;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="NC-15"></a>[NC-15] `public` functions not called by the contract should be declared `external` instead

*Instances (3)*:
```solidity
File: /script/deploy/Deploy.V2.s.sol

36:     function run() public returns (Contracts memory) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/script/deploy/Deploy.V2.s.sol)

```solidity
File: /src/core/Vault.kerosine.sol

36:     function deposit(uint id, uint amount) public onlyVaultManager {

47:     function getUsdValue(uint id) public view returns (uint) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

### <a name="NC-16"></a>[NC-16] Variables need not be initialized to zero
The default value for variables is zero, so initializing them to zero is superfluous.

*Instances (4)*:
```solidity
File: /src/core/Vault.kerosine.unbounded.sol

50:         for (uint i = 0; i < numberOfVaults; i++) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

181:         for (uint i = 0; i < numberOfVaults; i++) {

204:         for (uint i = 0; i < numberOfVaults; i++) {

218:         for (uint i = 0; i < numberOfVaults; i++) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | `decimals()` is not a part of the ERC-20 standard | 5 |
| [L-2](#L-2) | Division by zero not prevented | 1 |
| [L-3](#L-3) | External calls in an un-bounded `for-`loop may result in a DOS | 3 |
| [L-4](#L-4) | Initializers could be front-run | 1 |
| [L-5](#L-5) | Solidity version 0.8.20+ may not work on other chains due to `PUSH0` | 6 |
| [L-6](#L-6) | Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership` | 4 |
| [L-7](#L-7) | Upgradeable contract not initialized | 1 |
### <a name="L-1"></a>[L-1] `decimals()` is not a part of the ERC-20 standard
The `decimals()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*Instances (5)*:
```solidity
File: /src/core/Vault.kerosine.unbounded.sol

56:                 (10 ** vault.asset().decimals()) /

57:                 (10 ** vault.oracle().decimals());

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

117:             10 ** _vault.oracle().decimals() /

118:             10 ** _vault.asset().decimals();

155:             (10 ** (_vault.oracle().decimals() + _vault.asset().decimals()))) /

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="L-2"></a>[L-2] Division by zero not prevented
The divisions below take an input parameter which does not have any zero-value checks, which may lead to the functions reverting when zero is passed.

*Instances (1)*:
```solidity
File: /src/core/Vault.kerosine.unbounded.sol

61:         return (numerator * 1e8) / denominator;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

### <a name="L-3"></a>[L-3] External calls in an un-bounded `for-`loop may result in a DOS
Consider limiting the number of iterations in for-loops that make external calls

*Instances (3)*:
```solidity
File: /src/core/VaultManagerV2.sol

182:             Vault vault = Vault(vaults[id].at(i));

205:             Vault vault = Vault(vaults[id].at(i));

219:             Vault vault = Vault(vaultsKerosene[id].at(i));

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="L-4"></a>[L-4] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (1)*:
```solidity
File: /src/core/VaultManagerV2.sol

60:     ) external initializer {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="L-5"></a>[L-5] Solidity version 0.8.20+ may not work on other chains due to `PUSH0`
The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new `PUSH0` op code. This op code may not yet be implemented on all L2s, so deployment on these chains will fail. To work around this issue, use an earlier [EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target) [version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version). While the project itself may or may not compile with 0.8.20, other projects with which it integrates, or which extend this project may, and those projects will have problems deploying these contracts/libraries.

*Instances (6)*:
```solidity
File: /script/deploy/Deploy.V2.s.sol

2: pragma solidity =0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/script/deploy/Deploy.V2.s.sol)

```solidity
File: /src/core/KerosineManager.sol

2: pragma solidity =0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.bounded.sol

2: pragma solidity =0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

2: pragma solidity =0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)

```solidity
File: /src/core/VaultManagerV2.sol

2: pragma solidity =0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

```solidity
File: /src/staking/KerosineDenominator.sol

2: pragma solidity =0.8.17;

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/staking/KerosineDenominator.sol)

### <a name="L-6"></a>[L-6] Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership`
Use [Ownable2Step.transferOwnership](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) which is safer. Use it as it is more secure due to 2-stage ownership transfer.

**Recommended Mitigation Steps**

Use <a href="https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol">Ownable2Step.sol</a>
  
  ```solidity
      function acceptOwnership() external {
          address sender = _msgSender();
          require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
          _transferOwnership(sender);
      }
```

*Instances (4)*:
```solidity
File: /script/deploy/Deploy.V2.s.sol

69:         kerosineManager.transferOwnership(MAINNET_OWNER);

90:         unboundedKerosineVault.transferOwnership(MAINNET_OWNER);

91:         boundedKerosineVault.transferOwnership(MAINNET_OWNER);

98:         vaultLicenser.transferOwnership(MAINNET_OWNER);

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/script/deploy/Deploy.V2.s.sol)

### <a name="L-7"></a>[L-7] Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*Instances (1)*:
```solidity
File: /src/core/VaultManagerV2.sol

60:     ) external initializer {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | `block.number` means different things on different L2s | 2 |
| [M-2](#M-2) | Centralization Risk for trusted owners | 6 |
### <a name="M-1"></a>[M-1] `block.number` means different things on different L2s
On Optimism, `block.number` is the L2 block number, but on Arbitrum, it's the L1 block number, and `ArbSys(address(100)).arbBlockNumber()` must be used. Furthermore, L2 block numbers often occur much more frequently than L1 block numbers (any may even occur on a per-transaction basis), so using block numbers for timing results in inconsistencies, especially when voting is involved across multiple chains. As of version 4.9, OpenZeppelin has [modified](https://blog.openzeppelin.com/introducing-openzeppelin-contracts-v4.9#governor) their governor code to use a clock rather than block numbers, to avoid these sorts of issues, but this still requires that the project [implement](https://docs.openzeppelin.com/contracts/4.x/governance#token_2) a [clock](https://eips.ethereum.org/EIPS/eip-6372) for each L2.

*Instances (2)*:
```solidity
File: /src/core/VaultManagerV2.sol

99:         idToBlockOfLastDeposit[id] = block.number;

112:         if (idToBlockOfLastDeposit[id] == block.number)

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol)

### <a name="M-2"></a>[M-2] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (6)*:
```solidity
File: /src/core/KerosineManager.sol

7: contract KerosineManager is Owned(msg.sender) {

18:     function add(address vault) external onlyOwner {

23:     function remove(address vault) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol)

```solidity
File: /src/core/Vault.kerosine.bounded.sol

25:     ) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol)

```solidity
File: /src/core/Vault.kerosine.sol

12: abstract contract KerosineVault is IVault, Owned(msg.sender) {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol)

```solidity
File: /src/core/Vault.kerosine.unbounded.sol

42:     ) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol)
