#!/usr/bin/env -S deno run --allow-net --allow-env --allow-read
import "https://deno.land/x/dotenv@v3.2.0/load.ts";
import { hmac } from "https://deno.land/x/hmac@v2.0.1/mod.ts"

const FTX_API_KEY = Deno.env.get("FTX_API_KEY")
const FTX_API_SECRET = Deno.env.get("FTX_API_SECRET")
const ACTION = Deno.args[0]
const SIDE = Deno.args[1]
const AMOUNT = Deno.args[2]

async function makeRequest(method, endpoint, body) {
  const ts = new Date().getTime().toString()
  const params = {
    method: method,
    headers: {
      "Content-Type": "application/json",
      "FTX-KEY": FTX_API_KEY,
      "FTX-TS": ts
    }
  }
  
  if(method === "POST") {
    var SIGN_MESSAGE = `${ts}${method}${endpoint}${JSON.stringify(body)}`
    params["body"] = JSON.stringify(body)
  } else {
    var SIGN_MESSAGE = `${ts}${method}${endpoint}`
  }

  params["headers"]["FTX-SIGN"] = hmac("sha256", FTX_API_SECRET, SIGN_MESSAGE, "utf8", "hex")
  
  return fetch(`https://ftx.com${endpoint}`, params)
    .then(r => r.json())
}

async function placeOrder(market, side, amount) {
  return makeRequest('POST', '/api/orders', {
    "market": market,
    "side": side,
    "price": null,
    "type": "market",
    "size": amount
  })
}

const balances = await makeRequest('GET', '/api/wallet/balances')
const ethbull_balance = balances.result.find(d => d.coin === "ETHBULL")
const bear_balance = balances.result.find(d => d.coin === "BEAR")

const markets = await makeRequest('GET', '/api/markets')
const ethbull_price = markets.result.find(d => d.name === "ETHBULL/USD")
const bear_price = markets.result.find(d => d.name === "BEAR/USD")

const ethbull_side = ethbull_balance.usdValue < bear_balance.usdValue ? "buy" : "sell"
const bear_side = bear_balance.usdValue < ethbull_balance.usdValue ? "buy" : "sell"

if(ACTION === "balance") {
  const total_usd = (ethbull_balance.total * ethbull_price.bid) + (bear_balance.total * bear_price.bid)
  const ethbull_amount =
    (total_usd / 2) /
    (ethbull_side === "buy" ? ethbull_price.ask : ethbull_price.bid) -
    ethbull_balance.total
  const bear_amount =
    (total_usd / 2) /
    (bear_side === "buy" ? bear_price.ask : bear_price.bid) -
    bear_balance.total
  console.log(await placeOrder(
    "ETHBULL/USD",
    ethbull_side,
    Math.abs(ethbull_amount)
  ))
  console.log(await placeOrder(
    "BEAR/USD",
    bear_side,
    Math.abs(bear_amount)
  ))
} else if(ACTION === "trade") {
  console.log(await placeOrder(
    "ETHBULL/USD",
    SIDE,
    AMOUNT / (SIDE === "buy" ? ethbull_price.ask : ethbull_price.bid)
  ))
  console.log(await placeOrder(
    "BEAR/USD",
   SIDE,
   AMOUNT / (SIDE === "buy" ? bear_price.ask : bear_price.bid)
  ))
} else {
  console.log("Enter an action: 'trade' or 'balance'")
}
