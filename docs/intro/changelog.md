# Changelog

### 2020-08-02
For various betoffer resources, the optional query-parameter `type` used to filter out bet offers of given bet offer type
 now allows filtering on multiple bet offer. The numeric bet offer types are comma separated.

### 2020-05-27
New Push message added, EventStateUpdated, when an 
event changes state to/from STARTED or NOT_STARTED.

### 2020-05-07
To the ListView resource, new optional query-parameter "useCombinedLive" added.
If set to true, live events are also affected when selecting bet offer through the
query parameter "category". Old default behaviour is that displayed bet offers
for live events are unaffected by that parameter.

### 2020-04-06
Added new resource /section/eventgroup/{eventGroupIds} to list events from different event groups.
For more details see [Section Resource](#/docs/restapi/listview/section)

### 2020-03-23
Resource /betoffer/event/{eventId} added `prePacks` to response. 
For more details about Pre Packs have a look at [betbuilder](#/docs/domain/betbuilder)

### 2020-02-27
On Event added `extraInfo`.

### 2020-02-25
For Offering Push API, the subscription limit for a client was raised from 20 to 40.

### 2020-01-29
Added OT_ANY_PARTICIPANT outcome type to Player Last Occurrence and Player Occurrence Number bet offers.

### 2020-01-15
On Event element added two new tags: `ENHANCED_PLACE_TERMS` and  `PRICEBOOST` 

### 2019-10-29
On Event element added new tag `AWAY_HOME` which indicates that the event name format has the away team before the home team. 

### 2019-10-24
On Event element added new tag `NEUTRAL_VENUE` which indicates the event is played on a neutral venue.

### 2019-10-14
Added OT_PLAYER_OWN_GOAL and OT_NO_GOAL outcome types to Player Last Occurrence  and  Player Occurrence Number
bet offer types.

### 2019-10-07
Added `home` indicator on event participants. True for "first" player in a match (not affected by neutral venue).
False for all participants in competition.

### 2019-08-27
Removed the top element `group` from /betoffer responses, since it was never set anyway.

### 2019-07-15
Added new bet offer type OCCURRENCE_METHOD. Along with some optional attributes were added
* Bet offer
** Crtierion - `occurrenceNumberInLifeTime` - Used for sorting of bet offers of type OCCURRENCE_METHOD. This number represents the sequence order of the occurrence type, within the lifetime.

* Outcome
** New element `occurrence` with the children:
** `occurrenceType` - What kind of occurrence this outcome represents
** `occurrenceTypeLabel` - Translation label of the occurrence type

### 2019-06-17
Added `participantType` on event participant.

### 2019-06-11
Market parameter now supports both country (ISO-3166) and region (ISO-3166-2). See [Localization and Markets](#/docs/restapi/localization) for details.

### 2019-05-08
Timestamps are now always returned in the format “yyyy-MM-dd’T’HH:mm:ssZ”

### 2019-04-11
On bet offer, the sub-field _criteria_ now contains four new optional attributes
* raceToValue - Numeric value, reflecting the number of occurrences for a participant to be the very first participant in the event to reach. 
* occurrenceType - Occurrence type criteria, what is counted. GOALS, CARDS, the possible values might be extended. If not known, the value is UNTYPED.
* occurrenceNumber - The number of occurrences of the occurrence type during the lifetime this bet offer applies to.
* lifetime - Time criteria, during what time period of the event this bet offer applies to. FULL_TIME, the possible values might be extended. If not known, the value is UNTYPED.

### 2019-03-26
For the resource /betOffer/selected if multiple event ids are requested but not all events exists in the offering,
the existing one will be returned. Previously this returned 404 if any of the requested events were missing. Now if all
requested event(s) are missing a 404 is still sent.

### 2019-02-26
The resource /betOffer/selected now handles events within different sports.

### 2019-02-25
For baseball handicap bet offers, the bet offer containing the tag MAIN_LINE is now the handicap line with favorite -1.5
(previously it was the bet offer with the most even line that was considered MAIN_LINE).

### 2019-02-18
The API no longer sets HTTP expire-headers.

### 2019-01-24
Support for new bet offer types, Player Occurrence Number, Player Last Occurrence and Player Occurrence Line. See 
[betoffer](#/docs/domain/betoffer)

### 2018-11-27
In ListView resource, now possible to filter on attribute `matches` or `competitions` even if querying for multiple sports (previously blocked).  

On Event element added new tag `BET_BUILDER` which indicates the event supports in-event-combinations.

### 2018-11-05
For requests to the listView resource with multiple term-branches (e.g. multiple sports), if some terms do not have
matching events, the resource now returns events for the other terms. The non matching terms are ignored.

Previously the whole request was rejected with HTTP status 406. Still if none of the term(s) are matching any
events, the resource responds with HTTP status 406.

### 2018-10-10
Resource listView/{sports}/{regions}/{leagues}/{participants}/{attributes} has a new attribute _starting-within_ to fetch events starting within a time period. 
For more details have a look at [ListView](#/docs/restapi/listview/overview)

### 2018-08-07
Bet offer response for bet offers of type Handicap and Over/Under will now have a tag _MAIN_LINE_ 
to indicate what bet offer (of same type and criterion) that has the most even odds. 
The tag will not be pushed.

### 2018-08-01
Event name in api response could also be of format Home vs Away. This is configured per sport for 
an operator in the offering.

### 2018-07-10
Added 'englishName' to category (along-side existing 'name') for category resource:

    /{offering}/category/{categoryGroupNames}/sport/{sportId}  get bet offer categories based on sport

### 2018-07-10
MatchClock now contains two new fields `minutesLeftInPeriod` and `secondsLeftInMinute` to support reverse match clock.
Reversed match clock is only available for sportId AMERICAN_FOOTBALL, ICE_HOCKEY and BASKETBALL.
Check the documentation in explorer for more details. The new fields has also been added to the MatchClockUpdatedResponse push message.

### 2018-05-29
Resource /betoffer/selected/{categoryGroupName}/event/{eventId} did not handle live/prematch transparently as intended, this is corrected.

### 2018-05-25
Feature to support American display format where Event names are displayed in Away @ Home format and score/bet labels are also flipped.
* On *Event* model, *tags* field has a new tag `AMERICAN_DISPLAY_FORMAT` to indicate that the *name* (based on player locale) is flipped.
* On *Outcomes* and *ResultOutcomes* in BetOfferResponse the *label* field (score based) is also flipped.

### 2018-05-07
Removed push message `AllBetOffersSuspendedResponse` since it was never used anyway (instead listen to 
`BetOfferStatusUpdatedResponse`, it will be sent for each bet offer on the event).

### 2018-04-26
For an outcome, the magic odds *1 000* (1.00) to indicate the outcome is suspended, is no longer used. Now the odds is
removed if it is 1 000. As before API user should look at the field `status` instead to determine if it is OPEN, CLOSED,
SUSPENDED or SETTLED.

### 2018-04-23
A new resource for retrieving odds ladder with fractional and american odds were added.
See [outcome](#/docs/domain/outcome)


### 2018-03-16
Initial version is released
