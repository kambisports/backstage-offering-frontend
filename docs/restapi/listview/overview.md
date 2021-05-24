# List View

The List View is a resource which should be used for end-user clients to display events and their major bet offers,
allowing various ways of filtering both events and bet offers. This resource should not be used for getting all events
or bet offers in the offering. For that use case, the /group resource should be used.
 
By default only events in-play (ongoing) or starting in the near future is included. A fast client should not be
bombarded with a lot of events if it will only show a few of them. Which events that are included is highly depending on the
term filtering in the URL path-request but also what kind of events are available. The term filtering and attribute-parameters
are described in detail below.

## Endpoints
To explore all the endpoints for list view go to [Explorer](#/explorer) under List View tab.

## Matches and Competitions (Outrights)
The List View has two separate modes based on event types, "Matches" and "Competitions". The automatically chosen event type
depends on requested sport(s), attribute-parameters and available events.

A response never includes both matches and competitions (except for in-play events).

#### Event Type - Matches
In event type "Matches" mode, the List View will show:
- in-play match and competition events, with each main bet offer (if that bet offer is open for live betting)
- followed by upcoming ***match*** events, with either combined set of bet offers or single bet offer (open for prematch
betting)

#### Event Type - Competitions
If in event type "Competitions" mode, the List View will show:
- in-play competition events including the main bet offer (if that bet offer is open for live betting). In-play match events
are only included when filtering on "competition sport" (see below). E.g. for football filtering, ongoing match events are not
included within Competition mode, while ongoing ice-hockey games within winter_olympic_games matches are displayed regardless
of event type mode.  
- followed by upcoming ***competition*** events, with each main bet offer (if that bet offer is open for prematch betting) 

#### Logic for choosing Event Type
If no specific event type is given as attribute (see below), one of the event types will be chosen based on the
following criteria of the events which are available within the term filtering (e.g. sport)
- if only match events, "Matches" will be the event type mode
- if only competition events, "Competition" will be the event type mode
- if the events are in different sports, "Matches" will be the event type mode
- if the events are in one of the competition sports (see below), "Competition" will be the event type mode
- otherwise if the the events are in one of the other non-competition sport, "Matches" will be the event type mode.

**Competitions sports**

The following sports are considered competition sports, i.e. will default to competition mode:

golf, wintersports, motorsports, athletics, trotting, cycling, swimming, non-sport, politics, winter_sports, winter_olympic_games

Events in other sports are defaulting to match mode.


## Number of events returned
There are some limits of number of returned events within one request, depending on how many there are within certain
future time periods. Also the headings are different depending on the number of events. This logic is called now-and-soon.

- If at least 100 events are “available” (in-play or will start within next 24 hours) for current offering and
filtering parameters, then show all events in-play or starting within next 24 hours.
- Otherwise (less than 100 events are "available" within 24 hours), then if at least 150 events are “available” within
next 7 days for current offering and filtering parameters, then show all events in-play or starting within next 7 days.
- Otherwise (less than 150 events are "available" within 7 days), show all events (no future limit) for current
offering and filtering parameters.


## Syntax

```
/offering/v2018/kambi/listView/{sports}/{regions}/{leagues}/{participants}/{attributes}.json
```

Replace "kambi" with the offering identifier.

### Terms sports/regions/leagues/participants - normalized text

The values of the sport, region, league, participant are represented as “normalized text”. For event
groups it is based on the names in lower-case for each event group. For participants it is based on the
default (english) translation text in lower-case for each participant. “Normalized text” means the letters
outside a-z, 0-9 are transformed to equivalent composed or decomposed form, according to
Unicode normalization NFD model as described in http://www.unicode.org/reports/tr15/tr15-23.html. White-spaces
and other special characters (non letters, non numerics and neither - or _) are replaced with _. E.g.
“Ibrahimović, Zlatan” becomes ibrahimovic__zlatan (note the double underscores, due to comma and whitespace).

These transformations might result in loss of information, leading to a more general filtering. E.g. assume
two different participants called Jönsson and Jonsson within same league, it is not possible to create a
filter-query to exclude only one of them.

```
/football/all/all/manchester_united
```
will give all football events any team called manchester united is involved in. Only restriction is that if league is
explicit given, then region must also be given, it can’t be filtered as wildcard "all".

### Attributes

The attributes are used to do a separate filtering on top of the term filtering. The valid attributes are:

- **matches** - if given, only in-play events and upcoming match events are returned
- **competitions** - if given, only in-play events and upcoming competition events are returned
- **streaming** - if given, only events which is or will have a live stream are shown
- **in-play** - if given, only events open for live is shown (includes also events not started yet)
- **starting-soon** - if given, only events starting within 4 hours are shown
- **upcoming-live** - if given, only non-started live events are shown
- **starting-from** - if given, only events starting after given time are shown (see below)
- **starting-within** - if given, only events starting between the ´from´ and ´to´ parameters will be returned (see below)

Multiple attributes can be combined by comma. Some combinations are not allowed (request will be rejected):

- starting-from and in-play
- upcoming-live and in-play
- starting-from and starting-soon
- starting-from and starting-within
- starting-within and in-play

**Starting-from**

All but starting-from is non-valued attributes. starting-from takes a timestamp as argument on the format
yyyyMMdd’T’HHmmssZ. E.g. `starting-from=20180329T123456+0200` (url encoded: `starting-from=20180329T123456%2B0200`)

**Starting-within**

When using starting-within is used together with request parameters ´from´ and ´to´. They are required to have the format yyyyMMdd’T’HHmmssZ. 
E.g. `from=20180329T123456+0200` (url encoded: `from=20180329T123456%2B0200`)

Events starting exactly at ´from´ time are included, but events starting at ´to´ are excluded. The within range can be at most
48 hours.

## Filtering on participant and updates
Be aware that if filtering on participant, when any new live event is received through Push API or other resources
(e.g. /open.json), it is difficult for a client to determine if that new event should be added to the list of events
for this specific participant or not. This is because the other resources do not contain sufficient participant
information to be able to compare it with the participant filter term.

It is safer to re-poll the List View resource itself once in a while when doing participant filtering to get accurate
information.

## Syntax validation
The backend does not make a full syntax validation, it is possible to construct filters with less or more
square brackets and get some kind of result back. Even not correctly opened or closed square brackets might be accepted.
Consider that result as undefined and behaviour might change.
