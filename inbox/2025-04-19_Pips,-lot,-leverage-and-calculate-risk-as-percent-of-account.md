---
date: 2025-04-19
type: fact
aliases:
  -
hubs:
  - "[[trade]]"
---

# Pips, lot, leverage and calculate risk as percent of account

## Main (base) currency

For example, in the pair EURUSD, USD is the main currency. In the pair USDJPY, JPY is the main currency.


## Pips & Lot

There are two types of price based on the main currency of the pair:
- main currency is USD/CHF/CAD/NZD/AUD --> 5 decimal places
- main currency is JPY --> 3 decimal places

### Point
The last digit of the price is called a point. Some brokers present points with small letters.

### Pip
The second last digit of the price is called a pip.

We'll use pips to calculate the profit/loss of a trade or even candle size instead of points.


## Lot

1 LOT ~ 100,000 units of the money

When we say buy 0.01 lot EURUSD, it means we are buying:

0.01 * 100,000 = 1,000 EUR

with (assuming current exchange rate of EURUSD is 1.16543)

1,000 EUR * 1.16543 USD/EUR = 1,165.43 USD
                    ^^^^^^^ yeah, EURUSD means USD per EUR...



## Leverage

But if our account has only $200, how to buy 0.01 lot EURUSD?

Simple, we use leverage.

Leverage is a loan from the broker to the trader. The broker allows the trader to control a larger position with a smaller amount of capital.

There are different leverage ratios in Forex:
- 1:10 (x10)
- 1:30 (x30, Euro's permission)
- 1:50 (x50)
- 1:200 (x200, recommended)
- 1:500 (x500, recommended)
- 1:1000 (x1000)
- 1:2000 (x2000)

European brokers allow a maximum leverage of 1:30, but there are many brokers unrelated to Europe that can be chosen.

Recommend 1:200 and 1:500 leverage, although there are higher leverage options, it is not recommended to use them because the risk is too high.


## Calculate Maximum LOT with Leverage

If you have $200 in your account and you want to use 1:200 leverage, now the EURUSD exchange rate is 1.16543, how much lot can you buy?

1,000 USD * 200 = 200,000 USD, this is the maximum amount you can control with 1:200 leverage.

100000 * 1.16543 EUR/USD = 115,643 EUR/USD, this means if you want to buy 1 LOT EUR, you need to spend 115,643 USD

200,000 USD / 115,643 USD = 1.728 LOT, now we can buy maximum 1.728 LOT EURUSD with 1:200 leverage and originally $200 in your account.


## Calculate pips to USD

If you have 1 pip profit on EURUSD:
- 1 lot ~ 10 USD
- 0.1 lot ~ 1 USD
- 0.01 lot ~ 0.1 USD

But if main currency is JPY, then:
- 1 lot ~ 8.6 USD


## Reward/Risk Ratio

When trading, you need to set *stoploss* and *takeprofit* by pips value (not $$ or other currencies).

Reward/Risk ratio should greater than 3:1 (or 2.5:1) to be a good trade. Which means 1 win = 3 losses.


## Calculate risk as percent of account

If you have $1000 in your account and you want to limit your risk to about 2% of your account and use 1:200 leverage and now stoploss is 20 pips, how much lot can you buy?

Fomula:
(%Risk x Total balance) / (Stoploss by pips x Dollar per pips if buy 1 lot)

(1000 USD * 0.02) / (20 pip * 10 USD/LOT) = 0.1 LOT


## Margin Level & Margin Call

Margin level is the ratio of equity to margin. It is expressed as a percentage.

Margin level(%) = (Your Equity / Used Margin/Loans) * 100%

if margin level is less than 100%, it means you are in margin call. You need to deposit more money to your account or close some positions to avoid margin call.

Balance: Your deposit money + money after 1 trade closed
Equity: Your balance + money of the trade/orders are opening

Because the equity is always changing when the trade is opening, so the margin level is also changing when the trade is opening.

