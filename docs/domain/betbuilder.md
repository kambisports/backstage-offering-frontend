# Bet Builder and Pre Pack Selections

## Bet Builder
Bet Builder allows players to create bet combinations within the same [event](#/docs/domain/event). For an event, it is
possible through the resource _onDemandPricing_, to get a list of [outcomes](#/docs/domain/outcome) that can be included
in a Bet Builder bet. By supplying one or more outcome IDs to the resource, the response will indicate if the provided 
outcomes are combinable with each other and also what other non-chosen outcomes in same event are possible to add to the
built combination. When two or more outcomes are supplied the resource will return the combination odds as well.

Adding a Bet Builder bet on the betslip and placing the bet is done through the Player API.

## Pre Pack Selections
In the event resource, popular combinations of pre-selected outcomes are included - pre-constructed Bet Builders.
One such pre-constructed Bet Builder combination is called _Pre Pack Selection_ and contains label, odds and status.
A group of "Pre Pack Selections" that are created by combining outcomes from two or more bet offers belonging to the
same event, are referred to as simply "Pre Pack".

### Example
For the two bet offers _1x2 Full Time_ and _Over/Under 2.5 Points Full Time_ for a given event, a Pre Pack containing 
the whole "outcome space" with six Pre Pack Selections may exist, each of them with their own odds.

| Odds              | Over 2.5 | Under 2.5 |
|-------------------|----------|-----------|
| 1 (Home team win) |    23.00 |     11.50 |
| X (Draw)          |    14.00 |      5.75 |
| 2 (Away team win) |     2.40 |      2.50 |

A Pre Pack does not have to contain the whole "outcome space" of Pre Pack Selections. A Pre Pack can contain just one single
Pre Pack Selection.

### Interaction with a Pre Pack Selection
Since a Pre Pack Selection is a pre-constructed Bet Builder, the player can modify it and add or remove outcomes. Then it
is no longer the original Pre Pack Selection and should be treated as a plain Bet Builder.
