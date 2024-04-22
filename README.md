

# DYAD audit details
- Total Prize Pool: $36,500 in USDC
  - HM awards: $28,800 in USDC
  - QA awards: $1,200 in USDC 
  - Judge awards: $3,600 in USDC
  - Lookout awards: $2,400 USDC
  - Scout awards: $500 in USDC
 
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-04-dyad/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts April 18, 2024 20:00 UTC
- Ends April 25, 2024 20:00 UTC

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-04-dyad/blob/main/4naly3er-report.md).



_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._
* DYAD Multisig controls most of the protocol right now.
* Unlicensing a vault can cause issues.

# Overview
DYAD is the first truly capital efficient decentralized stablecoin. Traditionally, two costs make stablecoins inefficient: surplus collateral and DEX liquidity. DYAD minimizes both of these costs through Kerosene, a token that lowers the individual cost to mint DYAD. 
# DYAD

![dyad](https://github.com/code-423n4/2024-04-dyad/blob/main/dyadlogo.jpg?raw=true)

## Contracts

```ml
core
├─ DNft — "A dNFT gives you the right to mint DYAD"
├─ Dyad — "Stablecoin backed by ETH"
├─ VaultManager - "Manage Vaults for DNfts"
├─ VaultManagerV2 - "VaultManager with flash loan protection"
├─ Vault - "Holds different collateral types"
├─ Licenser - "License VaultManagers or Vaults"
├─ KerosineManager - "Add/Remove Vaults to the Kerosene Calculation"

staking
├─ Kerosine - "Kerosene ERC20"
├─ KerosineDenominator
├─ Staking - "Simple staking contract"

periphery
├─ Payments
```

### Deployed Contracts

All on Ethereum Mainnet

| Contract | Address |
|----------|----------|
| DYAD             | [0x305B58c5F6B5b6606fb13edD11FbDD5e532d5A26](https://etherscan.io/address/0x305b58c5f6b5b6606fb13edd11fbdd5e532d5a26) |
| dNFT             | [0xDc400bBe0B8B79C07A962EA99a642F5819e3b712](https://etherscan.io/address/0xdc400bbe0b8b79c07a962ea99a642f5819e3b712) |
| Vault Manager v1 | [0xfaa785c041181a54c700fD993CDdC61dbBfb420f](https://etherscan.io/address/0xfaa785c041181a54c700fd993cddc61dbbfb420f) |
| wETH Vault       | [0xcF97cEc1907CcF9d4A0DC4F492A3448eFc744F6c](https://etherscan.io/address/0xcf97cec1907ccf9d4a0dc4f492a3448efc744f6c) |
| wstETH Vault     | [0x7aE80418051b2897729Cbdf388b07C5158C557A1](https://etherscan.io/address/0x7ae80418051b2897729cbdf388b07c5158c557a1)|

### Migration
The goal is to migrate from VaultManager to VaultManagerV2. The main reason is the need for a flash loan protection which makes it harder to manipulate the deterministic Kerosene price.
The whole migration is described in `Deploy.V2.s.sol`. The only transaction that needs to be done by the multi-sig after the deployment is licensing the new Vault Manager.

## Links

- **Previous audits:**  None
- **Documentation:** https://dyadstable.notion.site/DYAD-design-outline-v5-1-3fa96f99425e458abbe574f67b795145?pvs=4
- **Website:** https://www.dyadstable.xyz/
- **X/Twitter:** https://twitter.com/0xDYAD
- **Discord:** https://t.co/nzml0Wapkt

---

# Scope

*See [scope.txt](https://github.com/code-423n4/2024-04-dyad/blob/main/scope.txt)*

### Files in scope


| File   | Logic Contracts | Interfaces | SLOC  | Purpose | Libraries used |
| ------ | --------------- | ---------- | ----- | -----   | ------------ |
| [/src/staking/KerosineDenominator.sol](https://github.com/code-423n4/2024-04-dyad/blob/main/src/staking/KerosineDenominator.sol) | 1| **** | 14 | ||
| [/src/core/VaultManagerV2.sol](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/VaultManagerV2.sol) | 1| **** | 166 | ||
| [/src/core/Vault.kerosine.sol](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.sol) | 1| **** | 62 | |@solmate/src/utils/SafeTransferLib.sol<br>@solmate/src/tokens/ERC20.sol<br>@solmate/src/auth/Owned.sol|
| [/src/core/KerosineManager.sol](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/KerosineManager.sol) | 1| **** | 34 | |@openzeppelin/contracts/utils/structs/EnumerableSet.sol<br>@solmate/src/auth/Owned.sol|
| [/src/core/Vault.kerosine.bounded.sol](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.bounded.sol) | 1| **** | 42 | |@solmate/src/tokens/ERC20.sol|
| [/src/core/Vault.kerosine.unbounded.sol](https://github.com/code-423n4/2024-04-dyad/blob/main/src/core/Vault.kerosine.unbounded.sol) | 1| **** | 60 | |@solmate/src/tokens/ERC20.sol<br>@solmate/src/utils/SafeTransferLib.sol|
| [/script/deploy/Deploy.V2.s.sol](https://github.com/code-423n4/2024-04-dyad/blob/main/script/deploy/Deploy.V2.s.sol) | 1| **** | 87 | |forge-std/Script.sol<br>@solmate/src/tokens/ERC20.sol|
| **Totals** | **7** | **** | **465** | | |

### Files out of scope

*See [out_of_scope.txt](https://github.com/code-423n4/2024-04-dyad/blob/main/out_of_scope.txt)*

| File         |
| ------------ |
| ./script/Read.s.sol |
| ./script/deploy/DeployBase.s.sol |
| ./script/mock/transfer.wsteth.s.sol |
| ./src/core/DNft.sol |
| ./src/core/Dyad.sol |
| ./src/core/Licenser.sol |
| ./src/core/Vault.sol |
| ./src/core/Vault.wsteth.sol |
| ./src/interfaces/IAggregatorV3.sol |
| ./src/interfaces/IDNft.sol |
| ./src/interfaces/IDyad.sol |
| ./src/interfaces/IStaking.sol |
| ./src/interfaces/IVault.sol |
| ./src/interfaces/IVaultManager.sol |
| ./src/interfaces/IWETH.sol |
| ./src/interfaces/IWstETH.sol |
| ./src/params/DNftParameters.sol |
| ./src/params/Parameters.sol |
| ./src/periphery/Payments.sol |
| ./src/staking/Kerosine.sol |
| ./src/staking/Staking.sol |
| ./test/BaseTest.sol |
| ./test/ERC20Mock.sol |
| ./test/OracleMock.sol |
| ./test/Payments.t.sol |
| ./test/Vault.wsteth.t.sol |
| ./test/VaultManager.t.sol |
| ./test/VaultManagerHelper.t.sol |
| ./test/WETH.sol |
| ./test/fork/v2.t.sol |
|./src/core/VaultManager.sol|
| Totals: 31 |

## Scoping Q &amp; A

### General questions

| Question                                | Answer                       |
| --------------------------------------- | ---------------------------- |
| ERC20 used by the protocol              |       Kerosene, weth, wseth   |
| Test coverage                           | 33.64%                        |
| ERC721 used  by the protocol            |            DNFT             |
| ERC777 used by the protocol             |           None                |
| ERC1155 used by the protocol            |              None            |
| Chains the protocol will be deployed on | Ethereum |

### ERC20 token behaviors in scope

- The only tokens in scope are: weth, wsteth.
- Vulnerabilities related to these token behaviours are only considered valid if they actually exist in tokens which are used, i.e. Kerosene, weth, etc.

### External integrations (e.g., Uniswap) behavior in scope:


| Question                                                  | Answer |
| --------------------------------------------------------- | ------ |
| Enabling/disabling fees (e.g. Blur disables/enables fees) | No   |
| Pausability (e.g. Uniswap pool gets paused)               |  Yes   |
| Upgradeability (e.g. Uniswap gets upgraded)               |   Yes  |


### EIP compliance
None


# Additional context

## Main invariants

- TVL > DYAD total supply


## Attack ideas (where to focus for bugs)
* Manipulation of Kerosene Price.

* Flash Loan attacks.



## All trusted roles in the protocol

DYAD Multisig: 0xDeD796De6a14E255487191963dEe436c45995813


| Role                                | Description                       |
| --------------------------------------- | ---------------------------- |
| DYAD Multisig                          | Ability to: License new Vault Manager, License new Vaults, Change the kerosene denominator contract, Add new vaults to the Kerosene Manager               |


## Any novel or unique curve logic or mathematical models implemented in the contracts:

None


## Running tests


```bash
git clone https://github.com/code-423n4/2024-04-dyad.git
git submodule update --init --recursive
cd 2024-04-dyad
forge install
forge test
```
To run code coverage
```bash
forge coverage
```
To run gas benchmarks
```bash
forge test --gas-report
```

![Screenshot from 2024-04-18 17-44-08](https://github.com/code-423n4/2024-04-dyad/blob/main/screenshot1.png?raw=true)
![Screenshot from 2024-04-18 17-45-57](https://github.com/code-423n4/2024-04-dyad/blob/main/screenshot2.png?raw=true)
![Screenshot from 2024-04-18 17-46-17](https://github.com/code-423n4/2024-04-dyad/blob/main/screenshot3.png?raw=true)
![Screenshot from 2024-04-18 17-47-42](https://github.com/code-423n4/2024-04-dyad/blob/main/screenshot4.png?raw=true)
![Screenshot from 2024-04-18 17-48-09](https://github.com/code-423n4/2024-04-dyad/blob/main/screenshot5.png?raw=true)




## Miscellaneous
Employees of DYAD and employees' family members are ineligible to participate in this audit.
