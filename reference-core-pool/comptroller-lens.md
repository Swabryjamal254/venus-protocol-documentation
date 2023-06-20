# ComptrollerLens

The ComptrollerLens contract has functions to get the number of tokens that can be seized through liquidation, hypothetical account liquidity and shortfall of an account.

# Solidity API

### liquidateCalculateSeizeTokens

```solidity
function liquidateCalculateSeizeTokens(
        address comptroller,
        address vTokenBorrowed,
        address vTokenCollateral,
        uint actualRepayAmount
    ) external view returns (uint, uint)
```

computes the number of collateral tokens to be seized up on liquidation using:

- address of vToken Borrowed
- address of collateral
- actual repayment amount.

#### Parameters

| Name              | Type    | Description                                                         |
| ----------------- | ------- | ------------------------------------------------------------------- |
| comptroller       | address | address of comptroller                                              |
| vTokenBorrowed    | address | address of the vToken Borrowed                                      |
| vTokenCollateral  | address | address of collateral for vToken                                    |
| actualRepayAmount | uint    | repayment amount i.e amount to be repaid from total borrowed amount |

#### Return Values

| Name | Type | Description                          |
| ---- | ---- | ------------------------------------ |
| [0]  | uint | success indicator                    |
| [1]  | uint | number of collateral tokens to seize |

### liquidateCalculateSeizeTokens

```solidity
function liquidateCalculateSeizeTokens(address comptroller, address vTokenBorrowed, address vTokenCollateral, uint256 actualRepayAmount) external view returns (uint256, uint256)
```

Computes the number of collateral tokens to be seized in a liquidation event

#### Parameters

| Name              | Type    | Description                                                       |
| ----------------- | ------- | ----------------------------------------------------------------- |
| comptroller       | address | Address of comptroller                                            |
| vTokenBorrowed    | address | Address of the borrowed vToken                                    |
| vTokenCollateral  | address | Address of collateral for the borrow                              |
| actualRepayAmount | uint256 | Repayment amount i.e amount to be repaid of total borrowed amount |

#### Return Values

| Name | Type    | Description                                |
| ---- | ------- | ------------------------------------------ |
| [0]  | uint256 | A tuple of error code, and tokens to seize |
| [1]  | uint256 |                                            |

### liquidateVAICalculateSeizeTokens

```solidity
function liquidateVAICalculateSeizeTokens(address comptroller, address vTokenCollateral, uint256 actualRepayAmount) external view returns (uint256, uint256)
```

Computes the number of VAI tokens to be seized in a liquidation event

#### Parameters

| Name              | Type    | Description                                                           |
| ----------------- | ------- | --------------------------------------------------------------------- |
| comptroller       | address | Address of comptroller                                                |
| vTokenCollateral  | address | Address of collateral for vToken                                      |
| actualRepayAmount | uint256 | Repayment amount i.e amount to be repaid of the total borrowed amount |

#### Return Values

| Name | Type    | Description                                |
| ---- | ------- | ------------------------------------------ |
| [0]  | uint256 | A tuple of error code, and tokens to seize |
| [1]  | uint256 |                                            |

### getHypotheticalAccountLiquidity

```solidity
function getHypotheticalAccountLiquidity(address comptroller, address account, contract VToken vTokenModify, uint256 redeemTokens, uint256 borrowAmount) external view returns (uint256, uint256, uint256)
```

Computes the hypothetical liquidity and shortfall of an account given a hypothetical borrow
A snapshot of the account is taken and the total borrow amount of the account is calculated

#### Parameters

| Name         | Type            | Description                      |
| ------------ | --------------- | -------------------------------- |
| comptroller  | address         | Address of comptroller           |
| account      | address         | Address of the borrowed vToken   |
| vTokenModify | contract VToken | Address of collateral for vToken |
| redeemTokens | uint256         | Number of vTokens being redeemed |
| borrowAmount | uint256         | Amount borrowed                  |

#### Return Values

| Name | Type    | Description                                             |
| ---- | ------- | ------------------------------------------------------- |
| [0]  | uint256 | Returns a tuple of error code, liquidity, and shortfall |
| [1]  | uint256 |                                                         |
| [2]  | uint256 |                                                         |