# Polymarket Liquidity Rewards & Book Depth

Polymarket markets that pay liquidity rewards, joined with the live order book: reward pool, qualifying depth and estimated payout per market.

[![Run on Apify](https://img.shields.io/badge/Run%20on-Apify-0f9f74)](https://apify.com/datagrit/polymarket-reward-yield-screener) [![Docs](https://img.shields.io/badge/docs-getdatagrit.github.io-0e1726)](https://getdatagrit.github.io/polymarket-reward-yield-screener/)

**from $3.50 per 1,000 results + $10 per run (pay per result; the rate depends on your Apify plan).** Export as JSON, CSV or Excel, call it through the API, or schedule it on Apify.

## What it does

It lists the Polymarket markets that pay liquidity rewards and joins each one with its live order book. For every market you get the daily reward pool, the reward band and minimum order size, the capital already resting inside the band on the bid and ask side, the reward score of that book, and an estimate of what a quote of your own size would earn. It is built for market makers, quant traders and bot builders who want to know where a dollar of liquidity earns the most, without opening hundreds of market pages.

## Quick start

1. Open [Polymarket Liquidity Rewards & Book Depth on Apify Store](https://apify.com/datagrit/polymarket-reward-yield-screener) and click **Try for free**.
2. Fill in the input form (or paste the JSON below) and run it.
3. Download the dataset, or fetch it from the API.

```json
{
  "minDailyRewardUsd": 50,
  "maxItems": 50
}
```

## Input

| Field | Type | What it does |
|---|---|---|
| `searchTerms` | array | Optional. Keep markets whose question contains at least one of these words or phrases (case-insensitive), for example Fed, Bitcoin or Senate. Leave empty to screen every market that pays rewards. |
| `minDailyRewardUsd` | number | Keep markets whose total published liquidity reward is at least this many dollars per day. Raising it shortens the scan. 0 turns the filter off. |
| `minVolume24hUsd` | number | Keep markets with at least this much traded volume in the last 24 hours. 0 turns the filter off. |
| `maxQualifyingDepthUsd` | number | Keep markets where the capital already resting inside the reward band, on both sides together, is at most this many dollars. Use it to skip crowded markets. 0 turns the filter off. |
| `quoteSizeUsd` | number | Total capital you would commit to a two-sided quote in each market, bid and ask together. The estimate of your payout and yield is calculated for this amount. |
| `quoteDistanceCents` | number | How far from the midpoint your bid and ask would rest. Closer scores higher; a quote beyond the market's reward band earns nothing. 1 means one cent on each side. |
| `sortBy` | string | Order of the results before "Maximum results" is applied. |
| `maxItems` | integer | Stop after this many markets. |
| `proxyConfiguration` | object | Optional. The Polymarket public API does not need a proxy; leave it disabled. |

## Output

| Field | Type | Description |
|---|---|---|
| `found` | boolean | False only for the single status row emitted when no market matches the input. |
| `conditionId` | string | Unique Polymarket condition ID of the market (0x...). Empty only on the status row. |
| `marketId` | string | Numeric Polymarket market ID. |
| `question` | string | The market question as shown on Polymarket. Empty only on the status row. |
| `slug` | string | URL slug of the market. |
| `eventSlug` | string | URL slug of the event the market belongs to. |
| `sourceUrl` | string | Link to the market on polymarket.com. Empty only on the status row. |
| `firstOutcome` | string | Name of the first outcome token, usually Yes. Bid and ask fields describe the order book of this outcome. |
| `secondOutcome` | string | Name of the second outcome token, usually No. Its book mirrors the first outcome: a bid on one is an ask on the other. |
| `firstTokenId` | string | CLOB token ID of the first outcome, the key for the order book endpoint. |
| `secondTokenId` | string | CLOB token ID of the second outcome. |
| `endDate` | string | ISO 8601 time the market is scheduled to end, as published by Polymarket. Null when not published. |
| `volume24hUsd` | number | Traded volume over the last 24 hours in dollars. Null when not published. |
| `priceChange1d` | number | Change of the first outcome price over the last day, in dollars (0.03 is 3 cents). Null when not published. |
| `rewardDailyUsd` | number | Total liquidity reward Polymarket publishes for this market per day, summed over its reward programs, as published. |
| `rewardMaxSpreadCents` | number | Maximum distance in cents from the midpoint at which an order still earns rewards. |
| `rewardMinSizeShares` | number | Smallest order in shares that earns rewards. |
| `rewardStartDate` | string | Earliest start date of the reward programs of the market (YYYY-MM-DD). Null when not published. |
| `bestBid` | number | Highest bid of the first outcome, in dollars (0.35 is 35 cents). |
| `bestAsk` | number | Lowest ask of the first outcome, in dollars. |
| `midpoint` | number | Midpoint between best bid and best ask, in dollars. Computed from the order book read by the Actor. |
| `spreadCents` | number | Best ask minus best bid, in cents. |
| `qualifyingBidShares` | number | Upper bound on shares resting on the bid side inside the reward band: levels of the aggregated book whose total size meets the minimum are counted in full, although the book does not show whether each single order meets it. |
| `qualifyingAskShares` | number | Upper bound on shares resting on the ask side inside the reward band: levels of the aggregated book whose total size meets the minimum are counted in full, although the book does not show whether each single order meets it. |
| `qualifyingBidUsd` | number | Upper bound on the capital behind the qualifying bids: price times size, over levels of the aggregated book that meet the minimum size. |
| `qualifyingAskUsd` | number | Upper bound on the capital behind the qualifying asks, counted as (one minus price) times size, the amount needed to rest the matching bid on the other outcome. |
| `qualifyingBidLevels` | integer | Number of price levels of the aggregated bid book inside the band whose total size meets the minimum. A level can hold several orders; the book does not show them separately. |
| `qualifyingAskLevels` | integer | Number of price levels of the aggregated ask book inside the band whose total size meets the minimum. A level can hold several orders; the book does not show them separately. |
| `qualifyingDepthUsd` | number | Upper bound on the capital already competing for the reward, bid and ask together. The book is aggregated per price level, so orders smaller than the minimum that share a level with others are counted too. |
| `rewardScoreBid` | number | Upper bound on the bid score: sum over qualifying bid levels of ((band - distance) / band)^2 times size, the scoring formula Polymarket documents for liquidity rewards. |
| `rewardScoreAsk` | number | The same upper-bound score for the ask side. |
| `rewardScorePool` | number | Score of the whole visible book combined from both sides: the larger of the smaller side and one third of the larger side when the midpoint is between 0.10 and 0.90, otherwise the smaller side. |
| `rewardPoolEmpty` | boolean | True when no visible order scores inside the band, so the estimate fields are null: there is no competitor in the book to measure your share against. Such markets are listed after all others. Null only on the status row. |
| `rewardPerDayPer1000QualifyingUsd` | number | Daily reward divided by the capital already quoting inside the band, per 1,000 dollars. Null when nothing qualifies yet. |
| `minQuoteCapitalUsd` | number | Smallest two-sided capital that qualifies at your quote distance: the minimum order size times (one minus twice the distance). |
| `quoteSizeUsd` | number | The two-sided capital from your input that the estimate is calculated for. |
| `quoteQualifies` | boolean | True when your quote capital buys at least the minimum order size and your quote distance is inside the reward band. When false, the estimate fields are null. |
| `estimatedSharePct` | number | Your estimated share of the reward if you added the quote, treating the visible book as a single competitor. A ranking aid, not a forecast. Null when the quote does not qualify or when no visible order scores inside the band. |
| `estimatedDailyRewardUsd` | number | Estimated share times the daily reward. Null when the quote does not qualify or when no visible order scores inside the band. |
| `estimatedDailyYieldPct` | number | Estimated daily reward as a percentage of your quote capital. Null when the quote does not qualify or when no visible order scores inside the band. |
| `scrapedAt` | string | ISO 8601 timestamp of extraction. |

Sample record:

```json
{
  "found": true,
  "conditionId": "0x12aa13b3da17ceae1b0a59b5d5b77121e91bb79b4bb0b52bf6543ed3f8d0953b",
  "marketId": "2589813",
  "question": "Will the Fed increase interest rates by 25 bps after the October 2026 meeting?",
  "slug": "will-the-fed-increase-interest-rates-by-25-bps-after-the-october-2026-meeting-20260617190324032",
  "eventSlug": "fed-decision-in-october-20260617190323537",
  "sourceUrl": "https://polymarket.com/event/fed-decision-in-october-20260617190323537/will-the-fed-increase-interest-rates-by-25-bps-after-the-october-2026-meeting-20260617190324032",
  "firstOutcome": "Yes",
  "secondOutcome": "No",
  "firstTokenId": "55159722761418013044126414276680602270318000841690689684819994448621694923050",
  "secondTokenId": "78632395083310670734315205502061249602319069653915932320155846460019107676757",
  "endDate": "2026-10-29T03:59:00.000Z",
  "volume24hUsd": 791285.96,
  "priceChange1d": -0.06,
  "rewardDailyUsd": 1000,
  "rewardMaxSpreadCents": 2.5,
  "rewardMinSizeShares": 200,
  "rewardStartDate": "2026-09-17",
  "bestBid": 0.35,
  "bestAsk": 0.36,
  "midpoint": 0.355,
  "spreadCents": 1,
  "qualifyingBidShares": 191549.72,
  "qualifyingAskShares": 93603.89,
  "qualifyingBidUsd": 65640.07,
  "qualifyingAskUsd": 58527.6,
  "qualifyingBidLevels": 3,
  "qualifyingAskLevels": 3,
  "qualifyingDepthUsd": 124167.67,
  "rewardScoreBid": 56769.99,
  "rewardScoreAsk": 10936.8,
  "rewardScorePool": 18923.33,
  "rewardPoolEmpty": false,
  "rewardPerDayPer1000QualifyingUsd": 8.05,
  "minQuoteCapitalUsd": 196,
  "quoteSizeUsd": 1000,
  "quoteQualifies": true,
  "estimatedSharePct": 1.9,
  "estimatedDailyRewardUsd": 19.04,
  "estimatedDailyYieldPct": 1.904,
  "scrapedAt": "2026-10-01T09:27:52.510Z"
}
```

## Call it from code

Runnable examples are in [`examples/`](examples). Replace `YOUR_APIFY_TOKEN` with the token from your Apify account settings.

```bash
curl -X POST "https://api.apify.com/v2/acts/datagrit~polymarket-reward-yield-screener/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"minDailyRewardUsd":50,"maxItems":50}'
```


## More from datagrit

- [Football Contract Expiry Radar - Transfermarkt](https://github.com/getdatagrit/football-contract-expiry-radar) - Football players whose contracts end soon, per league and expiry window, with market value, trend, agent and extension options from Transfermarkt end-of-contract lists.
- [Kalshi Settlement Calendar & Order Book Depth](https://github.com/getdatagrit/kalshi-settlement-calendar) - Kalshi markets ranked by when they settle, with the order book depth, spread and settlement source of each one.
- [TED Contract Expiry Radar - Recompete Leads](https://github.com/getdatagrit/ted-contract-expiry-radar) - Find EU public contracts approaching expiry from TED award notices: incumbent, buyer, value, end date and renewal options.
- [UK Contract Expiry Radar - Recompete Leads](https://github.com/getdatagrit/uk-contract-expiry-radar) - UK public contracts ending soon with incumbent supplier, buyer, value and contact - recompete leads from Contracts Finder award notices.
- [French Company Finder - Sirene Financials](https://github.com/getdatagrit/french-company-finder) - French company lead lists from Sirene screened by net result and revenue, with net margin, size, matching establishment and optional directors.

All Actors: [https://getdatagrit.github.io/](https://getdatagrit.github.io/) · [Apify Store](https://apify.com/datagrit)

---

This repository holds documentation and usage examples. Questions, bug reports and feature requests: use the **Issues** tab of the Actor page on [Apify Store](https://apify.com/datagrit/polymarket-reward-yield-screener). Examples are MIT licensed.
