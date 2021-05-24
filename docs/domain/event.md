# Events

An _event_ corresponds to a single happening that has a set of possible outcomes
and for which there are different bet offers. There are two types of events: matches
and competitions. A football game is a match, the Football World Cup is a competition.
In addition to sports events it is also possible to place bets for political and
other public events like president elections or Eurovision Song Contest. See the
related resources.

An event can have any number of [bet offers](#/docs/domain/betoffer).

Events do not contain other events. Events are instead grouped in [event groups](#/docs/domain/event_group).

All events has a number of mandatory fields such as a unique ID, a name and a [sport](#/docs/restapi/sports)
but also many fields that are specific to some aspects of the event that might require more detailed information.
See the [Explorer](#/explorer) for more information.


## Tags

Events contain a list of tags describing it.

Values might include but are not limited to:

| Tag          | Description |
|--------------|-------------|
|  `OFFERED_LIVE`      | The event will be offered live.                              |
|  `STREAMED_WEB`      | Event will be streamed to web (desktop) clients.             |
|  `STREAMED_MOBILE`   | Event will be streamed to mobile clients.                    |
|  `PREMATCH_STATS`    | Event has statistics.                                        |
|  `VISUALIZATION`     | Event will have live visualization.                          |
|  `OPEN_FOR_LIVE`     | The event is open for live betting.                          |
|  `SHOW_START_NUMBER` | A hint for clients to show start number on participants, mainly used in racing competitions. |
|  `MATCH`             | The event is a match (usually between two participants)      |
|  `COMPETITION`       | The event is a competition (usually leagues, tournaments etc.|
|  `AMERICAN_DISPLAY_FORMAT` | Deprecated, used AWAY_HOME instead.                    |
|  `BET_BUILDER`       | Bet builder bets are offered for the event.                  | 
|  `LIVE_OCCURRENCE_FEED` | The event will have live occurrence api feed.             | 
|  `NEUTRAL_VENUE`     | Event is played on a neutral venue                           | 
|  `AWAY_HOME`         | Indicates that the event name format has the away team before the home team.    | 


## State

An important aspect of an event is its state. The state impacts many things such as what
bet offers are available and at what odds. It also might affect availability of streaming and visualisation.

The states are:
  * NOT_STARTED - Before the event has started.
  * STARTED     - When the event has started but not yet been settled.
  * FINISHED    - When the event is settled.

More detailed states might be added later.

_Note:_ The Offering API does not keep FINISHED events for long. Some results can be accessible through Statistics API.
