# ✨ So you want to run an audit

This `README.md` contains a set of checklists for our audit collaboration.

Your audit will use two repos: 
- **an _audit_ repo** (this one), which is used for scoping your audit and for providing information to wardens
- **a _findings_ repo**, where issues are submitted (shared with you after the audit) 

Ultimately, when we launch the audit, this repo will be made public and will contain the smart contracts to be reviewed and all the information needed for audit participants. The findings repo will be made public after the audit report is published and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the audit sponsor (⭐️)**.

---

# Audit setup

## 🐺 C4: Set up repos
- [ ] Create a new private repo named `YYYY-MM-sponsorname` using this repo as a template.
- [ ] Rename this repo to reflect audit date (if applicable)
- [ ] Rename audit H1 below
- [ ] Update pot sizes
  - [ ] Remove the "Bot race findings opt out" section if there's no bot race.
- [ ] Fill in start and end times in audit bullets below
- [ ] Add link to submission form in audit details below
- [ ] Add the information from the scoping form to the "Scoping Details" section at the bottom of this readme.
- [ ] Add matching info to the Code4rena site
- [ ] Add sponsor to this private repo with 'maintain' level access.
- [ ] Send the sponsor contact the url for this repo to follow the instructions below and add contracts here. 
- [ ] Delete this checklist.

# Repo setup

## ⭐️ Sponsor: Add code to this repo

- [ ] Create a PR to this repo with the below changes:
- [ ] Confirm that this repo is a self-contained repository with working commands that will build (at least) all in-scope contracts, and commands that will run tests producing gas reports for the relevant contracts.
- [ ] Please have final versions of contracts and documentation added/updated in this repo **no less than 48 business hours prior to audit start time.**
- [ ] Be prepared for a 🚨code freeze🚨 for the duration of the audit — important because it establishes a level playing field. We want to ensure everyone's looking at the same code, no matter when they look during the audit. (Note: this includes your own repo, since a PR can leak alpha to our wardens!)

## ⭐️ Sponsor: Repo checklist

- [ ] Modify the [Overview](#overview) section of this `README.md` file. Describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the auditors should keep in mind when reviewing. (Here are two well-constructed examples: [Ajna Protocol](https://github.com/code-423n4/2023-05-ajna) and [Maia DAO Ecosystem](https://github.com/code-423n4/2023-05-maia))
- [ ] Review the Gas award pool amount, if applicable. This can be adjusted up or down, based on your preference - just flag it for Code4rena staff so we can update the pool totals across all comms channels.
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] [This checklist in Notion](https://code4rena.notion.site/Key-info-for-Code4rena-sponsors-f60764c4c4574bbf8e7a6dbd72cc49b4#0cafa01e6201462e9f78677a39e09746) provides some best practices for Code4rena audit repos.

## ⭐️ Sponsor: Final touches
- [ ] Review and confirm the pull request created by the Scout (technical reviewer) who was assigned to your contest. *Note: any files not listed as "in scope" will be considered out of scope for the purposes of judging, even if the file will be part of the deployed contracts.*
- [ ] Check that images and other files used in this README have been uploaded to the repo as a file and then linked in the README using absolute path (e.g. `https://github.com/code-423n4/yourrepo-url/filepath.png`)
- [ ] Ensure that *all* links and image/file paths in this README use absolute paths, not relative paths
- [ ] Check that all README information is in markdown format (HTML does not render on Code4rena.com)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# DYAD audit details
- Total Prize Pool: $36500 in USDC
  - HM awards: $28800 in USDC
  - (remove this line if there is no Analysis pool) Analysis awards: XXX XXX USDC (Notion: Analysis pool)
  - QA awards: $1200 in USDC
  - (remove this line if there is no Bot race) Bot Race awards: XXX XXX USDC (Notion: Bot Race pool)
 
  - Judge awards: $3600 in USDC
  - Lookout awards: XXX XXX USDC (Notion: Sum of Pre-sort fee + Pre-sort early bonus)
  - Scout awards: $500 in USDC
  - (this line can be removed if there is no mitigation) Mitigation Review: XXX XXX USDC (*Opportunity goes to top 3 backstage wardens based on placement in this audit who RSVP.*)
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-04-dyad/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts April 18, 2024 20:00 UTC
- Ends April 25, 2024 20:00 UTC

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-04-dyad/blob/main/4naly3er-report.md).



_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._
## 🐺 C4: Begin Gist paste here (and delete this line)





# Scope

*See [scope.txt](https://github.com/code-423n4/2024-04-dyad/blob/main/scope.txt)*

### Files in scope


| File   | Logic Contracts | Interfaces | SLOC  | Purpose | Libraries used |
| ------ | --------------- | ---------- | ----- | -----   | ------------ |
| /src/core/VaultManagerV2.sol | 1| **** | 262 | |@solmate/src/utils/FixedPointMathLib.sol<br>@solmate/src/tokens/ERC20.sol<br>@solmate/src/utils/SafeTransferLib.sol<br>@openzeppelin/contracts/utils/structs/EnumerableSet.sol<br>@openzeppelin/contracts/proxy/utils/Initializable.sol|
| /src/core/Vault.kerosine.bounded.sol | 1| **** | 42 | |@solmate/src/tokens/ERC20.sol|
| /src/core/Vault.kerosine.sol | 1| **** | 62 | |@solmate/src/utils/SafeTransferLib.sol<br>@solmate/src/tokens/ERC20.sol<br>@solmate/src/auth/Owned.sol|
| /src/core/Vault.kerosine.unbounded.sol | 1| **** | 60 | |@solmate/src/tokens/ERC20.sol<br>@solmate/src/utils/SafeTransferLib.sol|
| /src/core/KerosineManager.sol | 1| **** | 42 | |@openzeppelin/contracts/utils/structs/EnumerableSet.sol<br>@solmate/src/auth/Owned.sol|
| /script/deploy/Deploy.V2.s.sol | 1| **** | 87 | |forge-std/Script.sol<br>@solmate/src/tokens/ERC20.sol|
| /src/staking/KerosineDenominator.sol | 1| **** | 14 | ||
| **Totals** | **7** | **** | **569** | | |

### Files out of scope

*See [out_of_scope.txt](https://github.com/code-423n4/2024-04-dyad/blob/main/out_of_scope.txt)*

| File         |
| ------------ |
| ./script/Read.s.sol |
| ./script/deploy/Deploy.All.Sepolia.s.sol |
| ./script/deploy/Deploy.Goerli.s.sol |
| ./script/deploy/Deploy.Kerosene.Vaults.s.sol |
| ./script/deploy/Deploy.Kerosine.Goerli.s.sol |
| ./script/deploy/Deploy.Kerosine.Mainnet.s.sol |
| ./script/deploy/Deploy.Mainnet.Kerosine.s.sol |
| ./script/deploy/Deploy.Mainnet.s.sol |
| ./script/deploy/Deploy.Payments.s.sol |
| ./script/deploy/Deploy.Staking.Goerli.s.sol |
| ./script/deploy/Deploy.Staking.sol |
| ./script/deploy/Deploy.Vault.Goerli.s.sol |
| ./script/deploy/Deploy.Vault.Mainnet.s.sol |
| ./script/deploy/DeployBase.All.s.sol |
| ./script/deploy/DeployBase.Kerosine.sol |
| ./script/deploy/DeployBase.Staking.s.sol |
| ./script/deploy/DeployBase.s.sol |
| ./script/mock/transfer.wsteth.s.sol |
| ./src/core/DNft.sol |
| ./src/core/Dyad.sol |
| ./src/core/Licenser.sol |
| ./src/core/Vault.sol |
| ./src/core/Vault.wsteth.sol |
| ./src/core/VaultManager.sol |
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
| Totals: 46 |

