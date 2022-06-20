# ethbtc

This script makes it easy to trade ETH/BTC using the FTX [leveraged tokens](https://ftx.com/markets/leveragedtokens) ETHBULL (3x long ETH) and BEAR (3x short BTC).

## Buying or selling ETH/BTC

After the 'trade' keyword, provide a side of the market to take and a dollar value.

`./ethbtc trade buy 10`
`./ethbtc trade sell 10`

## Re-balancing portfolio

This keyword automatically rebalances your portfolio between ETHBULL and BEAR, equalising their dollar value.

`./ethbtc balance`
