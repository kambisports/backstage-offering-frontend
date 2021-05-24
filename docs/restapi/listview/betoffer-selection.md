# List View - Bet offer selection

The term filtering and attributes, decides which ***events*** will be included in List View response.
That is described in [List View - Overview](#/docs/restapi/listview).

For each event, there is a list of ***bet offers*** included. Not all bet offers belonging to that event are displayed in the
List View (e.g. not uncommon that really big events have a total of more than 1 000 bet offers). Bet offers
of the most popular [categories](#/docs/domain/betoffer?anchor=bet-offer-categories) are included in the response.

Which categories of bet offers that are included are determined by multiple reasons
* what bet offers are available
* types of the events (matches or competitions)
* sports of the events (all events are of same sport, or different)
* states of the events (pre-match or in-play)

The optional parameters ```category```, ```useCombined``` and ```useCombinedLive``` can also control what bet offers
are included.

@[toc]

## Matches - Single sport

### Matches - Single sport - Default behaviour
If all the events belongs to the very same sport, then by default all events will include the very same bet offer
category - the most interesting available one. Almost always the *main bet offer* unless none of the events have that bet offer
available.

If none of the events have a main bet offer available, the resource will give bet offers of another
popular bet offer category. All events will have the same bet offer category.

Events which have no bet offer belonging to the category, will be included but have an empty list as its bet offers.

### Matches - Single sport - Selecting specific bet offer category
It is possible to specify what bet offer category the events should include, by using the query parameter ```category```. The
allowed values of the parameter ```category``` are dynamic. They are retrieved as children within the element ```categoryGroups```
in the response of the List View itself (see below).

Typical use case is to first invoke the List View, without the ```category``` parameter and then let the end-user have
the possibility to select another bet offers of another category, e.g. by selecting in a drop-down among the alternatives given within
```categoryGroups``` element.

Events which have no bet offer belonging to the requested ```category```, will be included but have an empty list
as its bet offers.

If no such bet offers are available at all for any event, all events will have an empty list as its bet offers. This is
rare since ```categoryGroups``` element only includes bet offer categories which has available bet offers in the offering.
Might occur if bet offer was available, but then taken off site before the "second" request.

Only a sub-set of all *criterion* of bet offers are available to select through category parameter (see below).


### Matches - Single sport - Combined layout
Most *match sports*, supports something called *Combined Layout*. That means that if the query parameter
```useCombined``` is set to true the response may include up to three bet offers for the
very same event. That is the major bet offer categories that are configured for that sport, e.g. for football (soccer)
currently there are two categories configured "Match" and "Total goals".

Some match sports (e.g. boxing) has no combined layout, then it will only include one single (main) bet offer.

It is possible to select a specific bet offer category using the parameter ```category``` even when
```useCombined``` or ```useCombinedLive``` is set to true. If the category is among the standard bet offer categories of the
combined layout, then still all bet offers within the combined layout is included, not just the one matching the
requested category. (The reason is that in mobile client it might only be space to display one category anyway, or if
resizing desktop client.) If the requested ```category``` is not referring to a bet offer category within the combined layout
for the sport, then only bet offers which category matches that given ```category``` will be included (no combined layout).

#### useCombined and extending with useCombinedLive

##### useCombined
If ```useCombined``` is set to true, then for the pre-match events the included bet offers belongs to the combined layout and/or if
parameter ```category``` is given, that bet offer category.

Events that are in-play will include the single main bet offer for each event if that bet offer is available. Those
in-play events which has no main bet offer in the offering, will be included but have an empty list as its bet offers.
The parameter ```category``` has no affect of what bet offers are included for the in-play events when using ```useCombined```.

Available categories (see below) are only based on the bet offers available for the pre-match events.

##### useCombinedLive
If both ```useCombined``` and ```useCombinedLive``` is set to true, then both pre-match and in-play events get bet offers that
belongs to the combined layout and/or if parameter category is given, that bet offer category.

Available categories (see below) are based on bet offers available for both the pre-match and in-play events.

It is not allowed to set only ```useCombinedLive``` to true without setting ```useCombined``` to true.


### Different bet offers of the same type
An event can have multiple bet offers of the same type (*criterion*). Typically _Place_ (with different positions) or _Over/Under_
(with different lines).

The only kind of place bet offer which is included, is *Winner*. Other positions are excluded from List View to avoid confusion
for the end-user.

For Over/Under and Handicap the bet offer with the most even odds line is included, which means that a re-request of the resource may include
another Over/Under bet offer for an event, because the odds has changed or bet offer has been made available/unavailable.

One exception is Handicap bet offers for ice-hockey, the puck line (underdog + 1.5) betoffer is the only included,
never the most even line. Those ice-hockey events which do not have that specific line, will be included but have an
empty list as its bet offers.

## Competitions - Single sport
If the events are competitions, then each event will only include one single (main) bet offer per event.
For most sports that are the *Winner* bet offer. Those events which has no main bet offer in the offering, will be included
but have an empty list as its bet offers.

The query parameters ```useCombined```, ```useCombinedLive``` and ```category``` are ignored if the events are competition events.


## Multiple sports - Matches or Competitions
If the events belongs to different sports, each events' main bet offer (different criterion types per sport) will be the only bet
offers included. Those events which has no main bet offer in the offering, will be included but have an empty list as its bet offers.

The query parameters ```useCombined```, ```useCombinedLive``` and ```category``` are ignored if the events belongs to
different sports.


## Available and active categories
Except events and bet offers, the response also contains information on what bet offer categories are currently displayed
and what can be passed on to the ```category``` parameter.

### Available category groups and categories
The response will include the element ```categoryGroups``` which is a hierarchy of category groups and the
```categories``` within each categoryGroup. They contain information about what bet offer categories are available to
filter, including the id-value that can be passed to the ```category``` query parameter.

Only categories (and their category groups) for bet offer categories that are available in the offering for at least one event among the
events in the response, are populated in this hierarchy.

But as mentioned above, not each and every offered bet offer category for the events are included in the hierarchy, only
the popular ones that Kambi has pre-configured for being accessible in List View.

### Active categories
The element ```activeCategories``` is a list of selected/visible bet offer categories. Usually this will match the value of the
query parameter ```category```, but not necessarily. ```activeCategories``` indicates the categories of the currently
included bet offers, or empty if the response was just fall back to a default main bet offer category.

If no bet offers are available within the events of a given ```category```, the response will choose another category
which has at least one bet offer among the listed not-started events, and then active categories will not match the
requested category.

If ```useCombined``` is set to true, but ```category``` is not set, then ```activeCategories``` will contain
the categories of the bet offers available in the standard combined layout for the events.

### Translations
The text translations for each categoryGroup and category are available within the categoryGroups hierarchy.
