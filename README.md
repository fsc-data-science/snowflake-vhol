# Snowflake Virtual Hands on Lab

## What is a Swap Transaction

Check out this Token Swap on Ethereum. The NPC token for the Wrapped Ether token.

[https://etherscan.io/tx/0x54f08392df2b3572c0e3879a9c3121f05a36fb0cce5a70e4bad8eafda94fdd4e](https://etherscan.io/tx/0x54f08392df2b3572c0e3879a9c3121f05a36fb0cce5a70e4bad8eafda94fdd4e)

| Field                       | Data                                                                   |
|-----------------------------|------------------------------------------------------------------------|
| Transaction Hash            | 0x54f08392df2b3572c0e3879a9c3121f05a36fb0cce5a70e4bad8eafda94fdd4e |
| Status                      | Success                                                                |
| Block                       | 17990939 (352298 Block Confirmations)                                  |
| Timestamp                   | 49 days 8 hrs ago (Aug-25-2023 09:53:47 AM +UTC)                       |
| Transaction Action          | Swap 24,796,238.688165528889484169 NPC for 1.764996461796505157 ETH   |
| Platform                    | Uniswap V2                                                             |
| From                        | 0x893CC3BeE790245Cc5F9d81c883D2eBe3AAAdA5d                              |
| Interacted With (To)        | 0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D (Uniswap V2: Router 2)      |
| ERC-20 Tokens Transferred:  | 2                                                                      |
| 1st Token Transfer - From   | 0x893CC3...3AAAdA5d                                                    |
| 1st Token Transfer - To     | Uniswap V2: NPC 56                                                      |
| 1st Token Transfer - Amount | 24,796,238.688165528889484169 NPC ($1,538.36)                          |
| 2nd Token Transfer - From   | Uniswap V2: NPC 56                                                      |
| 2nd Token Transfer - To     | 0x893CC3...3AAAdA5d                                                    |
| 2nd Token Transfer - Amount | 1.764996461796505157 ETH ($2,726.11)                                   |

## What does it look like in EZ_DEX_SWAPS?

| Field               | Data                                                                      |
|---------------------|---------------------------------------------------------------------------|
| BLOCK_NUMBER        | 17990939                                                                  |
| BLOCK_TIMESTAMP     | 2023-08-25 09:53:47.000                                                   |
| TX_HASH             | 0x54f08392df2b3572c0e3879a9c3121f05a36fb0cce5a70e4bad8eafda94fdd4e   |
| ORIGIN_FROM_ADDRESS | 0x893cc3bee790245cc5f9d81c883d2ebe3aaada5d                                |
| PLATFORM            | uniswap-v2                                                                |
| CONTRACT_ADDRESS    | 0x69c7bd26512f52bf6f76fab834140d13dda673ca                                |
| POOL_NAME           | NPC-WETH UNI-V2 LP                                                        |
| TOKEN_IN            | 0x8ed97a637a790be1feff5e888d43629dc05408f6                                |
| SYMBOL_IN           | NPC                                                                       |
| AMOUNT_IN           | 24796238.6881655                                                          |
| AMOUNT_IN_USD       | 2684.19                                                                   |
| TOKEN_OUT           | 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2                                |
| SYMBOL_OUT          | WETH                                                                      |
| AMOUNT_OUT          | 1.764996462                                                               |
| AMOUNT_OUT_USD      | 2919.69                                                                   |


```
select 
block_number, block_timestamp, tx_hash, 
origin_from_address,
platform,
contract_address, pool_name, 
token_in, symbol_in, amount_in, amount_in_usd,
token_out, symbol_out, amount_out, amount_out_usd
from ethereum.defi.ez_dex_swaps
where tx_hash = '0x54f08392df2b3572c0e3879a9c3121f05a36fb0cce5a70e4bad8eafda94fdd4e'
 -- Sample Data is August 2023
AND  block_timestamp >= '2023-08-01'
AND  block_timestamp < '2023-09-01'
```

## What is a DEX Platform?

A DEX Platform is a set of `smart contracts` often created and/or managed by a Decentralized Autonomous Organization, 
to enable people to make trades of tokens without a centralized group (e.g., Coinbase or Binance) coordinating or brokering 
a buyer for each seller. 

Instead, people individually provide tokens to a `liquidity pool` such that anyone can pay them a fee to make a token trade.

Two popular platforms are [Uniswap](https://app.uniswap.org/swap) and [Curve](https://curve.fi/#/ethereum/swap). Uniswap specializes in crypto to crypto token trades for example Wrapped Bitcoin and Wrapped Ether, while Curve specializes in `stablecoins`, for example Circle USD and Tether USD tokens. Although nowadays both platforms have significant overlaps in tokens and stablecoins trading through their `liquidity pools`. Note: Uniswap has both `v2` and `v3` platforms, where `v3` 

```
select distinct(platform)
from ethereum.defi.ez_dex_swaps
 -- Sample Data is August 2023
where block_timestamp >= '2023-08-01'
AND block_timestamp < '2023-09-01'
order by platform ASC
```

## How do you measure Swap Volume in USD?

Our specific trade of `24,796,238.6881655` NPC tokens is worth `2,684.19` USD using the `ez_hourly_token_prices`
for that token in that hour.

| Field                        | Data                                                  |
|------------------------------|-------------------------------------------------------|
| HOUR                         | 2023-08-25 09:00:00.000                               |
| TOKEN_ADDRESS                | 0x8ed97a637a790be1feff5e888d43629dc05408f6             |
| SYMBOL                       | NPC                                                   |
| PRICE                        | 0.00010825                                            |
| OUR_SPECIFIC_TRADE_USD_VALUE | 2,684.19                                              |

```
select 
hour, token_address, symbol, price,
ROUND(price * 24796238.688, 2) as our_specific_trade_usd_value
 from ethereum.price.ez_hourly_token_prices
where token_address= '0x8ed97a637a790be1feff5e888d43629dc05408f6'
AND hour = '2023-08-25 09:00:00.000'
```

## Uniswap vs Curve who has more volume?

Looking at all trades on the platforms, Curve does more 
volume than Uniswap v2, but less than Uniswap v3.

| PLATFORM   | USD_VOLUME         |
|------------|--------------------|
| uniswap-v3 | 13,759,835,329.05  |
| curve      | 3,812,162,103.72   |
| uniswap-v2 | 2,416,483,067.82   |

```
select 
platform,
sum(amount_in_usd) as usd_volume
from ethereum.defi.ez_dex_swaps 
where platform IN ('uniswap-v2', 'uniswap-v3', 'curve')
AND -- Sample Data is August 2023
 block_timestamp >= '2023-08-01'
AND block_timestamp < '2023-09-01'
group by platform
order by usd_volume DESC;
```

## Bonus: Dex Volume vs Dex Token Price

| HOUR                     | SYMBOL | PRICE  |
|--------------------------|--------|--------|
| 2023-08-01 01:00:00.000  | CRV    | 0.558  |
| 2023-08-31 23:00:00.000  | CRV    | 0.45254|
| 2023-08-01 01:00:00.000  | UNI    | 6.57   |
| 2023-08-31 23:00:00.000  | UNI    | 4.35   |

```
select hour, symbol, price from ethereum.price.ez_hourly_token_prices
where token_address IN  (
   LOWER('0xD533a949740bb3306d119CC777fa900bA034cd52'),  -- CRV
   LOWER('0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984') -- UNI
     )
AND hour IN (
'2023-08-01 01:00:00.000',
'2023-08-31 23:00:00.000'
)
order by symbol, hour ASC
```
