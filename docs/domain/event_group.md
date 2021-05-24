# Event Group

Event groups are used to organize all events into a tree structure. An event group may contain
[events](#/docs/domain/event) or other child event groups. The _event group tree_ has three levels, where most
events are located in the lowest third level.

The tree structure is a basic instrument for browsing domain model. See the related resources.

The event groups are normally organized as _Sport - Region (country) - League_, but there are exceptions
where event groups and events are structured in another way in the Kambi domain. One cannot assume
that an event is always located under a Sport - Region - League, see below.

## Structure examples

### Standard structure
Football Premier League matches, are located as events under _Football - England - Premier League_
                  
    Football
        |
        +--- England
               |
               +--- Premier League
                          |
                          +--- Manchester City vs Arsenal (event)
                          |
                          +--- LiverPool vs Manchester United (event)

### Non-standard structure

League is a location:
- tennis ATP tournament in Rotterdam, are located under _Tennis - ATP - Rotterdam_
- horse races in Ascot, UK are located under _Horse Racing - UK - Ascot_

Only two levels, no level three event group:
- boxing fight between two well-known boxers, the event is located at level two, under _Boxing - Upcoming Fights_

First level is not a sport:
- football male matches in summer olympics, are located under _Olympic Games - Football - Male_ 
