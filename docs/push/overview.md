# Introduction

The Offering Push API is a web socket API to be consumed by client for getting various updates of the Offering.

The messages are sent when event occurs like odds is changed, new bet offer is added etc, so a client is not required
to poll the Offering API to discover changes in the offering. There are different types of messages describing different
kinds of updates. 

The Offering Push API is using Socket.io which is a JavaScript library for realtime web applications. For more
details on how to connect using the Socket.io client see https://www.socket.io.

@[toc]

### Routing key 
After connecting to the server the client must subscribe to one or more routing keys to receive messages. In socket.io
it is similar to sending messages to different rooms. To subscribe to updates for a single specific sportsbook event, the client
should send the routing key for that event, (e.g. 'v2018.kambi.en.ev.4525743.json') to the room 'subscribe'. When a client
do not want to receive more message for a subscription, it should send the same routing key to the room 'unsubscribe'.

All routing keys are prefixed with the version (v2018) followed by the mandatory offering identifier. The messages that
contains localized content (e.g. outcome label), are sent to subscribers using routing keys with the language identifier.

There are six different types of routing keys:
 * General channel for a specific language
 * General channel (non-localized)
 * Event specific channel for a specific language 
 * Event specific channel (non-localized)
 * Extended channel for a specific language (not available by default, must ask to be enabled)
 * Extended channel (non-localized) (not available by default, must ask to be enabled)
 
The table below contains messages associated with each routing key. Messages sent on routing keys including the
'lang' parameter, may contains localized strings in that given language. The supported languages depends on what has been
configured by Kambi for given offering identifier. Also see [Localization and Markets](#/docs/restapi/localization) 

### Usage
For a regular client it always makes sense to subscribe to the two general channels (specific language and non-localized).

Whenever extensive information is wanted about a specific event (e.g. see all its bet offers and statistics), the client
should subscribe to the two event specific channels (specific language and non-localized). 


### Deflate and bulking
The Offering Push API supports the `permessage-deflate` extension. It is specified in [RFC7692](https://tools.ietf.org/html/rfc7692).
The `permessage-deflate` compression is negotiated when connecting and will be used only if both server and client support it.

If multiple messages are sent to the same routing key within a short time period, they might be appended
together into one single message (e.g. two odds updates on same bet offer).

### Subscriptions limit
The client should unsubscribe to a routing key once it is no longer interested in the data. A client is responsible for
unsubscribing. E.g. if the corresponding event is finished and gone from the Offering API, the client must still unsubscribe.
Same if the client allows the user to navigate away from an event, it should make sure to unsubscribe to the event specific
channels.

A client is only allowed to subscribe to at most 40 different routing keys at the same time. Past that, its
following subscribe requests, will be ignored, until it unsubscribes.

When the subscription limit is reached an error message will be returned to the client:
```
{
    "code": 429,
    "message": "Subscription denied, max subscriptions 40 reached. Please unsubscribe to some topics",
    "topic": "v2018.kambi.ev.10000.json"
}
```

### Live push only and polling HTTP Offering API
Only changes that affects the live offering is sent through the Offering Push API. There is no push for changes
related to pre-match offering, since they occur long time before an event actually starts.

A client is still advised to combine the Offering Push API with some sort of polling mechanism towards the normal
HTTP based Offering API as well, specially for clients which are using not reliable connections (e.g. mobile phones)
and might drop their connection to the Offering Push API. Otherwise the client might show obsolete odds etc,
without the end-user being aware of bad or lost connection.

## List of routing keys, pushed messages and their Message type identifiers

To get more detailed information about each message type, see the [Push Explorer](#/push_explorer) which explains their
models and provides a REST API to view example messages based on real data. If real data is currently not available
in the offering to construct an example message (in particular when using test environment), the example message will
be using mocked data.

### General channel (specific for a language)
_Routing key:_ v2018.{offering}.{lang}.ev.json 

|       Message         | Message type  | Message element | Comment                                  | Example                                                                                   |
|-----------------------|---------------|-----------------|------------------------------------------|-------------------------------------------------------------------------------------------|
| EventAdded            |       4       | _ea_            |                                          | [Example](/offering/v2018/kambi/push-examples/EventAdded?lang=en_GB)                      |
| MatchClockUpdated     |      15       | _mcu_           |                                          | [Example](/offering/v2018/kambi/push-examples/MatchClockUpdated?lang=en_GB&mockData=true) |
| LiveEventOpened       |       5       | _leo_           |                                          | [Example](/offering/v2018/kambi/push-examples/LiveEventOpened?lang=en_GB)                 |
| BetOfferAdded         |       6       | _boa_           | only sent for main & list view bet offer | [Example](/offering/v2018/kambi/push-examples/BetOfferAdded?lang=en_GB)                   |
| BetOfferOddsAdded     |      22       | _booa_          | only sent for main & list view bet offer | [Example](/offering/v2018/kambi/push-examples/BetOfferOddsAdded?lang=en_GB)               |
| BetOfferStatusUpdated |       8       | _bosu_          | only sent for main & list view bet offer | [Example](/offering/v2018/kambi/push-examples/BetOfferStatusUpdated?lang=en_GB)           |

### General channel (non-localized)
_Routing key:_ v2018.{offering}.ev.json

|       Message           | Message type  | Message element | Comment                                  | Example                                                                                                 |
|-------------------------|---------------|-----------------|------------------------------------------|---------------------------------------------------------------------------------------------------------|
| EventRemoved            |      18       | _er_            |                                          | [Example](/offering/v2018/kambi/push-examples/EventRemoved)                                             |
| MatchClockRemoved       |      12       | _mcr_           |                                          | [Example](/offering/v2018/kambi/push-examples/MatchClockRemoved)                                        |
| EventScoreUpdated       |      16       | _score_         |                                          | [Example](/offering/v2018/kambi/push-examples/EventScoreUpdated?mockData=true)                          |
| EventStatsUpdated       |      17       | _stats_         | only sent for set based event            | [Example](/offering/v2018/kambi/push-examples/EventStatsUpdated?mockData=true&eventStatsType=SET-BASED) |
| EventDescriptionUpdated |      20       | _desc_          |                                          | [Example](/offering/v2018/kambi/push-examples/EventDescriptionUpdated)                                  |
| BetOfferRemoved         |       7       | _bor_           | only sent for main & list view bet offer | [Example](/offering/v2018/kambi/push-examples/BetOfferRemoved)                                          |
| BetOfferOddsUpdated     |      11       | _boou_          | only sent for main & list view bet offer | [Example](/offering/v2018/kambi/push-examples/BetOfferOddsUpdated)                                      |
| BetOfferOddsRemoved     |      23       | _boor_          | only sent for main & list view bet offer | [Example](/offering/v2018/kambi/push-examples/BetOfferOddsRemoved)                                      |
| EventStateUpdated       |      34       | _esu_           |                                          | [Example](/offering/v2018/kambi/push-examples/EventStateUpdated)                                        |

### Event specific channel (for a specific language)
_Routing key:_ v2018.{offering}.{lang}.ev.{id}.json

|       Message           | Message type  | Message element | Example                                                                                 |
|-------------------------|---------------|-----------------|-----------------------------------------------------------------------------------------|
| TickerUpdated           |      19       | _tu_            | [Example](/offering/v2018/kambi/push-examples/TickerUpdated?lang=en_GB&mockData=true)   |
| BetOfferAdded           |       6       | _boa_           | [Example](/offering/v2018/kambi/push-examples/BetOfferAdded?lang=en_GB)                 |
| BetOfferOddsAdded       |      22       | _booa_          | [Example](/offering/v2018/kambi/push-examples/BetOfferOddsAdded?lang=en_GB)             |
| BetOfferStatusUpdated   |       8       | _bosu_          | [Example](/offering/v2018/kambi/push-examples/BetOfferStatusUpdated?lang=en_GB)         |
| MatchOccurrence         |      25       | _mo_            | [Example](/offering/v2018/kambi/push-examples/MatchOccurrence?lang=en_GB&mockData=true) |


### Event specific channel (non-localized)
_Routing key:_ v2018.{offering}.ev.{id}.json

|       Message           | Message type  | Message element | Comment                        | Example                                                                                                                          |
|-------------------------|---------------|-----------------|--------------------------------|--------------------------------------------------------------------------------------------------------|
| BetOfferRemoved         |       7       | _bor_           |                                | [Example](/offering/v2018/kambi/push-examples/BetOfferRemoved)                                         |
| BetOfferOddsUpdated     |       11      | _boou_          |                                | [Example](/offering/v2018/kambi/push-examples/BetOfferOddsUpdated)                                     |
| EventStatsUpdated       |       17      | _stats_         | only sent for football matches | [Example](/offering/v2018/kambi/push-examples/EventStatsUpdated?mockData=true&eventStatsType=FOOTBALL) |
| BetOfferOddsRemoved     |       23      | _boor_          |                                | [Example](/offering/v2018/kambi/push-examples/BetOfferOddsRemoved)                                     |
| EventScoreUpdated       |       16      | _score_         | only for tennis game           | [Example](/offering/v2018/kambi/push-examples/EventScoreUpdated?mockData=true)                         |
| VisualizationOccurrence |       27      | _vo_            |                                | [Example](/offering/v2018/kambi/push-examples/VisualizationOccurrence?mockData=true)                   |
| LiveStatistics          |       28      | _ls_            |                                | [Example](/offering/v2018/kambi/push-examples/LiveStatistics?mockData=true)                            |
| OutcomesStatusUpdated   |       32      | _osu_           |                                | [Example](/offering/v2018/kambi/push-examples/OutcomesStatusUpdated)                                   |


## Push for settled outcomes

Although not related to Offering API but Player API, it also exists a push channel for settlement.
_Routing key:_  v2018.{offering}.bh.json

|       Message       | Message type  | Message element |Example                                                         |
|---------------------|---------------|-----------------|----------------------------------------------------------------|
| OutcomesSettled     |     24        | _os_            | [Example](/offering/v2018/kambi/push-examples/OutcomesSettled) |


## Extended general channels
For each of the two general channels (non-event specific) there are two extended versions.

The extended channels send the very same messages as the general channels, but beyond main & list view bet offer they
also send additional messages related to updates for other pre-selected bet offers as well (around 0-5 additional
criteria depending on sport).

These two extended channels are not available by default. Must be requested to Kambi to enable it, per offering.

### Extended general channel (for a specific language)
_Routing key:_ v2018.{offering}.{lang}.ext.ev.json
Same kind of messages as _General channel (specific for a language)_

### Extended general channel (non-localized)
_Routing key:_ v2018.{offering}.ext.ev.json
Same kind of messages as _General channel (non-localized)_

Note, those channels will include push messages related to bet offers having same criterion as bet offer having the tag
MAIN (and MAIN_LINE). It is the consumer's responsibility to determine how to act if bet offer that are considered
MAIN and MAIN_LINE changes. E.g. if a client is displaying bet offer marked as MAIN_LINE and that bet offer is removed
(BetOfferRemovedResponse is sent), then the client needs to decide if any other bet offer with same criterion should
be displayed instead.
