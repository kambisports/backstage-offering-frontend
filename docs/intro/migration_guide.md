# Migration Guide

This page describes the major differences in v2018 compared to the old v2/v3 Offering API.

Version 2018 - List of changes compared to v2 and v3
====================================================

@[toc]

## General

* The URL path now has the format `/v2018` (previously `/api/v2` and `/api/v3`)

* Statistics and feed-resources removed (neither was mentioned in the API documentation for v2 anyway)


## Resources (Removed, renamed and added)
The following resources have been removed:
  * `/offering/api/v2/kambi/group/featured/{groupId}.json`
  * `/offering/api/v2/kambi/betoffer/{betOfferId}.json`
  * `/offering/api/v2/kambi/betoffer/{betOfferId}/resultoutcome/{resultOutcomeId}.json`
  * `/offering/api/v2/kambi/betoffer/{betOfferId}/playeroutcome/{playerOutcomeId}/resultoutcome/{resultOutcomeId}.json`
  * `/offering/api/v2/kambi/betoffer/live/{betOfferId}.json`
  * `/offering/api/v2/kambi/betoffer/live/main/event/{eventId}.json`
  * `/offering/api/v2/kambi/betoffer/live/main/group/{groupId}.json`
  * `/offering/api/v2/kambi/betoffer/live/group/{groupId}.json`
  * `/offering/api/v2/kambi/betoffer/live/outcome.json`
  * `/offering/api/v2/kambi/betoffer/timespan/{hours}.json`
  * `/offering/api/v2/kambi/betoffer/main/timespan/{hours}.json`
  * `/offering/api/v2/kambi/betoffer/main/timespan/minutes/{minutes}.json`
  * `/offering/api/v2/kambi/betoffer/best/group/{groupId}.json`
  * `/offering/api/v2/kambi/betoffer/selected/{categoryViewName}/group/{groupId}.json`
  * `/offering/api/v2/kambi/betoffer/combined/group/{groupId}.json`
  * `/offering/api/v2/kambi/betoffer/live.json`
  * `/offering/api/v2/kambi/betoffer/search.json`
  * `/offering/api/v2/kambi/event/search.json`
  * `/offering/api/v2/kambi/event/livedata.json`
  * `/offering/api/v2/kambi/event/group/{groupId}/today`

* The following resource has been renamed (since it only shows events which will be offered live):
  `/offering/api/v2/kambi/event/calendar` renamed to `/offering/v2018/kambi/event/livecalendar`

* New resource for fetching active meetings for a sport (Horse racing and Greyhounds).
 `/offering/v2018/kambi/meeting/{sport}`

## Parameter changes (General)

* The parameter `include=PARTICIPANTS` that was on some resources is now changed to `includeParticipants=true`

* The parameter `path` that was on some resources is now removed. Affected resources in v2018: `/offering/api/v2/kambi/betoffer/event/{eventId}` and `/offering/api/v2/kambi/betoffer/participant/{participantId}`

* The parameter `cat` has been renamed to `category`. Affected resource in v2018: `/offering/api/v2/kambi/betoffer/group/{groupId}`

* The parameter `range_size` now has a maximum value of 2 000 in all resources.

## Response field changes (General)

* The order of the fields within an object might have changed.

* All resources that returns bet offers and events, have the field with the list of bet offers named `betOffers` (in v2 it was `betoffers`)

* For lists which contains no values, now empty list is returned (in v2 and v3 the fields where left out then)
  * event -> liveData -> liveStatistics,
  * betOffers -> prevOdds
  * betOffers -> eachWay -> tags
  * liveFeedUpdates (which is now actually liveData -> liveFeedUpdates)
  * tickers (which is now actually liveData -> tickers)

* All timestamps are returned in ISO8601 format, for example `2017-01-11T15:00:00Z`.

    Previously: 
  * timestamps as unix timestamps
    * Event -> start
    * Event -> prematchEnd
  * in ISO8601 but with minutes granularity only betoffers -> events -> start
  * in ISO8601 but with seconds granularity only betoffers -> closed
  
Exception is events -> originalStartTime which is just a description on the format "HH:mm" and not changed.


### Response fields (Event element)
* On event, the field `boUri` is removed (was only in v2, never existed in v3) from resources.

* On event, the field `eventNavigation` is removed in v2018 (was only in v2, never existed in v3) from resources.

* On event, the field `rank` is removed (was only in v3) from resources.

* On event, the field `hasLiveBetOffers` is renamed to `offeredLive`, since that is actually what it means.

* In events, the `liveData` object will always contains score (with empty `away`, `home`, and `who=UNKNOWN`). Previously it was omitted.

* Response from `/event/livedata` and `/event/<id>/livedata` resources are now wrapped in a response object containing a
single `liveData`-field.

* In events with `mainBetOffer`, the `order` field on `criterion` was earlier omitted when empty. Now the `order` field is always set
(with one element in the list with the value '0').

* In participants on events, the fields `nonRunner` and `scratched` are now always supplied, even if they are false. Previously left-out.

* Several flags and states on `Event`s and `LiveData` has moved to a list of tags.

  *Note*: Tags are not a fully defined set of values and new tags might appear without prior notice.

  The following fields on `Event` replaced by the specified tag:

  * `offeredLive`             ->  OFFERED_LIVE            - The event will be offered live.
  * `openForLiveBetting`      ->  OPEN_FOR_LIVE           - The event is open for live betting.
  * `hasPrematchStatistics`   ->  PREMATCH_STATS          - Event has statistics.
  * `hideStartNo`             ->  SHOW_START_NUMBER       - A hint for clients to show start number on participants (meaning of the old flag has been inverted). Mainly used in racing competitions.
  * `americanDisplayFormat`   ->  AMERICAN_DISPLAY_FORMAT - The event should be presented as "away @ home" 

  * flag `streamed` and list `streams` have been been replaced with tags:
    * STREAMED_WEB    - Event will be streamed to web (desktop) clients.
    * STREAMED_MOBILE - Event will be streamed to mobile clients.

  * `displayType` has been replaced with tags:
    * MATCH           - The event is a match (usually between two participants)
    * COMPETITION     - The event is a competition (usually leagues, races, tournaments etc)

  Two fields on `LiveData` are also moved to tags on `Event`:
    * `visualizationSupported`  ->  VISUALIZATION   - Event will have live visualization.
    * `openLive`                ->  OPEN_FOR_LIVE (see above)

### Response fields (Bet offer element)
* On bet offer the flag `cashIn` field is removed. Use `cashOutStatus` instead. 

* On bet offer, for the element `criterion`, the field `isDefault` is removed.

* Several flags and states on bet offers have moved to a list of tags.

  *Note*: Tags are not a fully defined set of values and new tags might appear without prior notice.

  The following fields on `BetOffer` replaced by the specified tag:

  * `open`            ->  field removed and no tag added. Please consider tags on `Event` in order to determine if the event is open for live
  * `live`            ->  OFFERED_LIVE        - The bet offer will be offered live.
  * `prematch`        ->  OFFERED_PREMATCH    - The bet offer will be offered prematch.
  * `main`            ->  MAIN                - The bet offer is the main bet offer within the event.
  * `combinable`      ->  NOT_COMBINABLE      - The bet offer can not be combined with other bet offers when placing bets. It tag is omitted the bet offer might be combined with other bet offers. Note that tag is negated compared to legacy api.
  * `startingPrice`   ->  STARTING_PRICE      - The odds of the bet offer will be set during settling. The odds for this bet offer will be returned as -1.
  * `pba`             ->  PBA_DISABLED        - Partial Bet Approval is not available for this bet offer. API no longer supplies a localized static description, instead it is up to the API consumer to decide text.
  * `rule4`           ->  RULE4               - Rule 4 applies to the bet offer. See the official terms and conditions for the description of Rule 4. API no longer supplies a localized static description, instead it is up to the API consumer to decide text.
  * `americanDisplayFormat`   ->  AMERICAN_DISPLAY_FORMAT - The bet offer should be presented as "away @ home" 
  * `mainLine`        ->  MAIN_LINE           - The odds of the outcome's for the bet offer are the most even within bet offer.

* For bet offer, previously outcome `odds` of magic *1 000*, meant the outcome should be treated as suspended. Now instead the
odds is no longer returned while the field `status` on the outcome is set to SUSPENDED. For SUSPENDED outcomes, the field `odds`
might be returned if it has a real value.

* Bet offers for started racing events now have `suspended` set to true to show that they are not bettable.

## Live Prematch merge
A merge of prematch and live concept has been done. A client should not be required to know in advance if an event is in
state prematch or live.

* changes in `/betoffer/outcome`. 
    * Removed *includeLive* query param.
    * A bet offer which is offered both prematch and live is no longer returned twice in response. It will have both the tags OFFERED_PREMATCH and OFFERED_LIVE. 
     
* Merged the behavior of following resources to `/offering/v2018/kambi/betoffer/group/{groupId}`
    * `/offering/v2018/kambi/betoffer/live/open/group/{groupId}`
    * `/offering/v2018/kambi/betoffer/main/group/{groupId}`
 
  Now you can use *excludeLive* and *excludePrematch* query parameters to get only prematch or live bet offers
  respectively. Also set *onlyMain=true* to get just the main bet offers. The default behavior of this resource is to
  return all bet offers including main, live and prematch.

* `/offering/v2018/kambi/event/group/{groupId}` now return events with both live and prematch bet offers.

* `/offering/v2018/kambi/group` now return groups with events that has both prematch and live bet offers, boCount and eventCount affected.

* `/offering/v2018/kambi/group/{id}` now return groups with events that has both prematch and live bet offers, boCount and eventCount affected.

- Removed resource `/offering/api/v2/kambi/betoffer/live/event/{eventId}.json`

   Has been merged into `/offering/v2018/kambi/betoffer/{eventId}.json which now returns both live and prematch bet offers.

- Removed resource `/offering/api/v2/kambi/betoffer/live/participant/{participantId}.json`

  Has been merged into `/offering/v2018/kambi/betoffer/participant/{participantId}.json`, which now returns both live and prematch bet offers.

- Removed resource `/offering/api/v2/kambi/event/live/participant/{participantId}.json`

  Has been merged into `/offering/v2018/kambi/event/participant/{participantId}.json`, which now returns both live and prematch bet offers.

- The `/offering/v2018/kambi/betoffer/mostpopular/3way.json` resource now as default return both live and prematch bet offers.
  You can use *excludeLive* and *excludePrematch* query parameters to get only prematch or live bet offers. 

- Merged the following resources into  `/offering/v2018/kambi/{categoryGroupNames}/sport/{sportId}.json`
  * `/offering/api/v2/kambi/category/{categoryViewName}/group/{groupId}`
  * `/offering/api/v2/kambi/group/{groupId}/category/{categoryViewName}`
 
  It returns all bet offer categories for one or many categoryGroups for a sport.


## Resource specific changes

### List View resource - `/offering/v2018/kambi/listView/...`

#### Parameter changes
  * Parameter `categoryGroup` is removed. The resource itself will now decide which category group is suitable.
  * New boolean parameter `useCombined`, defaults to false. If set to true the list view *may* include all bet offer
    types as a combined layout. By default only one bet offer type will be included in the response. 
  * Parameter `category` which allows to decide which bet offers to include in the response has changed. Now it only
    accepts the unique numeric ``id`` of the category as value, previously the value was the name of a category (which
    was not unique across different category groups).

  * Parameter `displayDefault` is removed (resource will return events of either matches/competitions based on sport of
    the events, unless attribute `matches` or `competitions` is given).  
  * Deprecated `betOffers` is removed (was earlier renamed to `categoryGroup` which is removed as well).
  * Deprecated `betOfferCategory` is removed (was earlier renamed to `category` though meaning has changed, see above).


#### Response field changes
  * The flat structure of categories and category groups of `categories` has been superseded by the new element
    `categoryGroups` which contains nodes of `categories` for each group.
  * The `activeCategoryIds` has been renamed to ```activeCategories```. Also if useCombined=true, all included bet
   offers categories are included. 

See [List View](#/docs/restapi/listview) for details.


### Landing page resource - `/offering/v2018/kambi/landingPage.json`
* Empty events list is now supplied for each card. Previously if a card was empty, the events were left out.
(Only exception in v2018 is that startingsoonaggregate which do not include empty events list).

* Landing page now default to not include participants. A new query parameter `includeParticipants` has been added to include participants.
