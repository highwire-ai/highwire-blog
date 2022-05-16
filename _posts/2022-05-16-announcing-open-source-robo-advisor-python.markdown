---
layout: post
title:  "Announcing an Open Source Robo-Advisor Library Written in Python"
date:   2022-05-16 12:18:14 -0700
---

[open-robo-advisor](https://github.com/highwire-ai/open-robo-advisor) is an open-source library of code written in Python. It's built to provide code for building a robo-advisor, which is an automated investment service that uses computer algorithms to manage your investments.

It's a work in progress, but it...

* **Works with any asset**\
  ETFs, mutual funds, stocks, crypto, bonds, ...
* **Supports asset targeting**\
  10% USD, 20% VXUS, 70% VTI
* **Understands asset classes**\
  5% cash, 5% crypto, 20% foreign stock, 70% domestic stock
* **Handles multiple currencies**\
  USD, CAD, BTC, ETH, ...
* **Complete transaction history**\
  Backed by an in-memory double-entry bookkeeping ledger

## Why Open Source Robo-Advisor?

The open-robo-advisor library is:

* Open to review and improvement by others
* Easy to use and learn from by new developers
* Flexible enough to work in countries where robo-advisors aren't available yet

## Using open-robo-advisor

Install open robo-advisor:

```
pip install open-robo-advisor
```

Start by creating a portfolio and adding an account to it.

```
portfolio = Portfolio()
account = portfolio.open_account('My Fidelity Account')
```

Set up the initial assets. In this case, our Fidelity account receives $2000, and buys 4.5177 shares of VTI.

```
account.deposit(2000)

account.buy(
    symbol='VTI',
    shares=Decimal('4.5177'),
    amount=1000,
    fees=Decimal('9.95'),
)
```

The account has $990.05 in cash and 4.5177 shares of VTI at this point. The $990.05 is what's left when you deposit $2000, then buy $1000 worth of VTI and pay a $9.95 fee.

We need to define asset targets so the advisor knows the desired balance. Since we're going to use SimpleAdvisor, we set the targets for assets (USD, VTI, VEA) rather than asset classes (cash, US stock, foregin emerging market).

```
account_targets = {
    'My Fidelity Account': {
        Currency('USD'): Decimal('0.02'),
        Security('VTI'): Decimal('0.45'),
        Security('VEA'): Decimal('0.15'),
        Security('VWO'): Decimal('0.15'),
        Security('VIG'): Decimal('0.09'),
        Security('VTEB'): Decimal('0.14'),
    }
}
```

SimpleAdvisor nees to know what assets are currently worth. Define a map from assets to current prices.

```
quotes = {
    Currency('USD'): 1,
    Security('VTI'): Decimal('221.17'),
    Security('VEA'): Decimal('47.79'),
    Security('VWO'): Decimal('47.82'),
    Security('VIG'): Decimal('158.08'),
    Security('VTEB'): Decimal('53.24'),
}
```

Instantiate the advisor and get its suggestions.

```
advisor = SimpleAdvisor(
    portfolio=portfolio,
    account_targets=account_targets,
    quotes=quotes,
)

suggestions = advisor.get_suggestions()
```

Suggestions look like this:

```
{'My Fidelity Account': [Sell(asset_type=Currency('USD'), amount=Decimal('950.26540582')),
                         Sell(asset_type=Security('VTI'), amount=Decimal('104.02633995')),
                         Buy(asset_type=Security('VEA'), amount=Decimal('298.38445635')),
                         Buy(asset_type=Security('VWO'), amount=Decimal('298.38445635')),
                         Buy(asset_type=Security('VIG'), amount=Decimal('179.03067381')),
                         Buy(asset_type=Security('VTEB'), amount=Decimal('278.49215926'))]}
```

## Next Steps

If you are interested in learning more about our project, please visit [GitHub](https://github.com/highwire-ai/open-robo-advisor). We’d love to hear your feedback on the library and how it could be improved.

If you are interested in contributing code to the project, feel free to fork our repository and send us a pull request when done with your changes.