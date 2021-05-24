# Outcome   
Outcome is one of the possible result on a [bet offers](#/docs/domain/betoffer) with associated odds. It describes the
result the punter is placing the bet on, this includes properties such as participant label. An example is home team to
win in a three way bet offer, another example is a Yes in a Yes/No bet offer.

@[toc]

## Status
| Status    | Description |
|-----------|-------------|
| OPEN      | Outcome is open and bettable* |
| CLOSED    | Outcome is closed and not bettable |
| SUSPENDED | Outcome is temporary taken offline and not bettable |
| SETTLED   | Outcome is settled |

A suspended outcome, means that it is not possible to place any bets on the outcome, but it can still be displayed.

### Effective Status
An outcome that has status OPEN should nevertheless be considered suspended if its [bet offer](#/docs/domain/outcome) is
suspended, i.e. has `suspended=true`. This is to optimize for push; if a bet offer is suspended/unsuspended there is no
need to push updates for all its outcomes.


## CashoutStatus
| CashOutStatus  | Description |
|----------------|-------------|
| ENABLED        | Might be possible to cash out outcome* |
| SUSPENDED      | Not possible to cash out outcome (temporarily) |
| DISABLED       | Not possible to cash out outcome |

A suspended cashoutStatus, means that it is currently not possible to cash out, but it might be only temporarily disabled.

### Effective CashOutStatus
To be able to cash out an outcome, not only does it need to have CashOutStatus=ENABLED, but also the betOffer the outcome
belongs to, must have CashOutStatus=ENABLED. This is to optimize for push; if a bet offer changes CashOutStatus
there is no need to push updates for all its outcomes. See table below.

| BO CashOutStatus | Outcome CashOutStatus | Effective CashOutStatus |
|------------------|-----------------------|-------------------------|
| ENABLED          | ENABLED               | ENABLED                 |
| ENABLED          | SUSPENDED             | SUSPENDED               |
| ENABLED          | DISABLED              | DISABLED                |
| SUSPENDED        | ENABLED               | SUSPENDED               |
| SUSPENDED        | SUSPENDED             | SUSPENDED               |
| SUSPENDED        | DISABLED              | DISABLED                |
| DISABLED         | ENABLED               | DISABLED                |
| DISABLED         | SUSPENDED             | DISABLED                |
| DISABLED         | DISABLED              | DISABLED                |

The normal Status (on both outcome and bet offer) does *not* need to be taken into consideration to resolve effective
CashOutStatus. The original CashOutStatus' from the Offering API for both bet offer and outcome, are already taken that
into account.

## Odds
Each outcome has always its odds represented in three formats; decimal, fractional and american.

| Field name      | Example | Description  |
|-----------------|---------|-------------------------------------------------------------------------------------|
| odds            |  4700   | Decimal odds, multiplied with 1,000 (should be presented as 4.70)                   |
| oddsFractional  | "18/5"  |                                                                                     |
| oddsAmerican    |  "370"  | Underdogs odds are not prefixed with '+', while favorite odds are prefixed with `-` |

### Real value vs Presentation value
Internally in Kambi platform only decimal odds are used for calculations for combination odds, cash out, price diff etc
and bet placement as well and various reports.

Fractional and american odds format are only for presentation for offered odds and placed bet history to punters who
prefer those formats.


### Fractional & American Odds


#### Beautification and be nice to the punter
Not every decimal odds is mapped one-to-one to its mathematically correct fractional or american odds.

There are some simplifications (rounding) to make the odds more comprehensible for the punter. E.g. decimal odds
1.51, is as fractional odds not represented as 51/100 (which is mathematically correct) but instead just 1/2. While odds
1.50 is of course shown as 1/2.

The fractional and american odds always show *equal or worse odds than the actual underlying decimal odds*.
E.g. decimal odds 1.49 is shown in fractional format as 12/25 (which mathematically equals decimal 1.48), never as 1/2.
A punter using fractional or american odds format will never be mislead that they will win more money than the odds
they see, only the opposite (in this case the payout will be based on odds 1.49).

This means that a range of consecutive decimal odds, might in fractional and/or american odds format be displayed
with identical value.


### Converting from Decimal to Fractional and American format
As mentioned above, the Offering API presents all three formats in the output. However when calculating odds
for combination bets, the client needs to do the conversation by itself for punters using fractional or american odds
format.


#### Resource converting table
To make it possible for the client to do this calculation and converting correctly, Offering API provides one separate
resource which lists the two mappings from decimal odds values (multiplied by 1,000) to fractional and from decimal odds
values (multiplied by 1,000) to american odds values.

`/offering/v2018/kambi/oddsLadder.json`

Although the mapping might be updated, a client should not invoke this resource for every punter,
rather distribute it at build time of the client or at least keep it in a global cache for a certain time.

See the "Odds ladder" section under [Explorer](#/explorer) for more information.

#### Mapping of values not in table
The two mapping tables in the odds ladder, does neither contain all possible decimals nor mappings to fractional and
american odds. It is the client's responsibility to handle gaps in the series and select a *lower* odds for fractional
and american odds (to be nice to the punter).

E.g. To display a fractional odds and the decimal odds is 1.99 (1990 in the API), and 1990 is not in the
odds ladder fractional mapping table, then closest lower available mapping should be used to convert to fractional odds.
If 1980 is available display its fractional mapping, if not the 1970 mapping should be used etc.

#### Special values outside table range

The mappings covers only converting from lower decimal odds to fractionals and american favorite odds. For higher decimal
odds, the API consumer that wants to display them as fractionals or american odds, needs to compute those formats.

##### Fractionals
Decimal odds above 30001.00 (30001000 in the API) should in fractional odds format be computed as
`trunc(decimal odds - 1) appended by "/1"`. E.g. decimal odds 37955.72 (37955720 in the API) should be rendered as "37954/1".


##### American
Decimal odds equal or above 2.00 (2000 in the API) - underdog odds - should in american odds format be computed as
`round(decimal odds * 100 - 100)`. E.g. decimal odds 37955.72 (37955720 in the API) should be rendered as "3795472"
(or "+3795472").
