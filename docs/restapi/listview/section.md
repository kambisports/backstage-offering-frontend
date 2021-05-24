# Section Resource

Section is a separate resource similar to List View, it should be used for end-user clients to display events and their major
bet offers. This resource should not be used for getting all events or bet offers in the offering. For that use case,
the /group resource should be used.

This resource includes in-play or upcoming events for the requested event group(s). Each requested event group is treated
separately. 


## Endpoint
To explore the section endpoint go to [Explorer](#/explorer) under Section tab.


## Syntax
```
/offering/v2018/{offering}/section/eventgroup/{eventGroupIds}?lang={language}&market={market}&useCombined=true&onlyMatches=true
```

Replace "kambi" with the offering identifier. "eventGroupIds" is a comma-separated list of numeric ids representing the 
requested event groups.


## Sections
Each requested event group id is divided into sections. Events within the event group and its sub event groups are considered.

Requested event groups which do not have any available events, will not have a section in the response. So each section in
the response will contain at least one event.

No sorting among of the sections, they may come in any order in the response. Each section contains sectionAttribute which
indicates which event group it represents.

If none of the requested event group(s) have any events, then the response will be an empty list of sections but with
HTTP status code 200. 

## Events within a section
The included events within each section are the ones that are closest to start time, but at most 10 events (possible to
change value through request parameter sectionMaxSize) is returned for each section. If multiple events starts at the
same time, they are sorted by event id. In case there are 11 events starting at the same time within the same event
group the resource will return the 10 with lowest event id.

Each requested event group gets its own section in the response. What events which are included in one section, does not
affect other sections. The sections are unrelated to each other. If any requested event groups are overlapping in the
hierarchy, same event might be included in multiple sections. 
 
Only match events are considered, no competitions (outrights). Pre-match events without any bet offers are ignored.
Meanwhile events open for live are considered, regardless if there are no bet offers available for that event. 

There is no fixed time limit (like ListView), so if the 10th event has a start time two years from now, it will still be included.
Only number of events in that section is the limitation.


## Included bet offers
For an event not all its bet offers are included in the response, only the major ones. See [Bet Offers](#/docs/restapi/listview/betoffer-selection.md).

One section contains the similar type of bet offers for all its events (except when those bet offers are taken off site).
Diverse sections can represent events of different sports, and then the sections will have different types of bet offers.  

If no major bet offer is available for an event, the response will have an empty list of bet offers for that event.

For match handicap bet offers, only the main line (most even / puck line / runline) will be included among the bet offers.
If that bet offer is not available for an event, that event wont contain any handicap bet offer in the response.

## LiveData
Events which are in-play also contains a liveData-element with live statistics about the event.

## Error handling
If mandatory parameter is not given or invalid value, HTTP status code will be 400.

If number of requested event groups are "too many" (number depends on performance and load) the request will be rejected
with HTTP status code 400.
