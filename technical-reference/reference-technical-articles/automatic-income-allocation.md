# Automatic income allocation

The purpose of this document is to explain the technical details for distributing incomes generated by the Venus Protocol. The distributed incomes are generated from the spread of lending rates (borrow minus supply rates) and liquidations (part of the liquidation incentives). The distribution process for both the Core Pool and Isolated Pools as well as how the different amounts are allocated to different destinations based on specific rules and the approach for near-streaming income distribution are covered.```

## Income Distribution Schema
We want to distribute the protocol incomes this way *[Tokenomics](https://snapshot.org/#/venus-xvs.eth/proposal/0xc9d270ccecb7b91c75b95b8d9af24fc7c20cd38c0c0c44888ed4e7724f4e7ce9)*

The Venus Protocol generates income from two main sources:

- (A) Spread between the borrowing rate and the supply rate
- (B) Part of the liquidation incentives

The distribution of protocol incomes is as follows:

**Schema 1:**

- When: Income generated by (A) from markets supported by the Prime program.
- Distribution:
    - 40%: Risk fund
    - 40%: Treasury
    - 10%: XVS Vault rewards
    - 10%: Venus Prime

**Schema 2:**

- When: Conditions for Schema 1 are not satisfied.
- Distribution:
    - 50%: Risk fund
    - 40%: Treasury
    - 10%: XVS Vault rewards

The initial distribution excludes VAI funds, which are kept in the VTreasury.

## Streaming Income Distribution

<figure><img src="../.gitbook/assets/autoamtic-income-allocation.png" alt=""></figure>

The Venus Protocol aims to distribute accumulated reserves in the markets automatically and in near-real-time, leveraging transactions executed by users. This approach eliminates the need for external tools to manage the distribution process. The `ProtocolShareReserve` contract serves as the designated destination for liquidation incomes, allowing for tracking and distribution within the Venus Protocol.

### Core Pool - Spread Income

Accumulated reserves in the Core Pool markets will be distributed after reaching a specified threshold. The threshold is based on the number of blocks since the last transfer of reserves. This strategy helps socialize part of the cost associated with distribution.

### Core Pook - Liquidation Income
In the Core Pool, liquidation income is transferred to the Liquidator contract in the form of vTokens. During a liquidation transaction, the Liquidator contract will try to redeem the protocol's portion of the liquidation incentive in vTokens for the underlying tokens. If the redemption process is successful, the  underlying tokens will be sent to the `ProtocolShareReserve` contract. However, if the redemption fails the underlying tokens will be added to a list of pending redemptions and the Liquidator contract will try to redeem the pending redemptions again in subsequent liquidation transactions.

### Isolated Pool - Spread Income

Distribution of income generated by the interest rate spread in Isolated Pools is socialized and distributed periodically just like in the Core Pool

### Isolated Pool - Liquidation Income

The Isolated Pools liquidations are managed by the VToken contracts. During a liquidation transaction, the protocol's percentage of the seized amount is transferred directly to the `ProtocolShareReserve` contract. No other action is required.

## Distribution of Collected Incomes

The distribution of collected incomes is facilitated by the `ProtocolShareReserve` contract. It supports WBNB transfers but not BNB transfers. Therefore, BNB needs to be wrapped into WBNB before transferring to the `ProtocolShareReserve` contract.

### `updateAssetsState` Function

The `updateAssetsState` function enables the transfer of funds to the `ProtocolShareReserve` contract. After transferring funds from the origins to the `ProtocolShareReserve` contract, the `updateAssetsState` function invoked with the following parameters:

- `address comptroller`: The Comptroller where the market generated the income.
- `address asset`: The asset transferred to the `ProtocolShareReserve`.
- `IncomeOrigin origin`: The origin type, either "spread" or "liquidation."

Within the `updateAssetsState` function, the following steps are performed:

- Calculate the transferred balance, taking into account the previous balance of the asset.
- Assign the received amount to the appropriate entry (comptroller - asset - schema).
- Track the total balance of the received asset for future distribution.

### `releaseFunds` Function

The `releaseFunds` function is responsible for distributing the accumulated funds to the different destinations. This function can be invoked by anyone and follows a two-step process:

1. Transfer the tokens to the destination addresses.
2. Invoke the `updateAssetsState` function in the receiver contract.

## VBNBAdmin

`VBNBAdmin` contract is the admin of the vBNB vToken. All the other vToken contracts send revenue to PSR directly whereas vBNB sends the reserves to the admin i.e., the vBNBAdmin contract. This pattern is different for vBNB because it's not a upgradable contract. 

Whenever  the `VBNBAdmin` receives BNB from the vBNB contract it converts it to WBNB and then sends it to the Protocol share reserve contract.

To release the reserves of vBNB contract you need to call the `reduceReserves()` function of the `VBNBAdmin` contract.