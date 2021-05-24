# List View - Advanced

Advanced term-filtering allows pre-constructing links to show the most interesting or specific sports, events
and participants.


## Non-multiple
Normal one-dimension filtering uses one term per segment /football/england/premier_league/arsenal or wild-carded using
the segment "all", like /football/all/all/arsenal.

## Multiple
It is possible to filter events on different branches of OR-queries using multiple values which will be returned
in same result list. This is accomplished by using comma between the event groups and/or participant terms and wrap
the fields with [ and ]. The attributes are applied to all queries (not possible to have different attributes for
different branches).


### Basic multiple (top level, two sports)
```
/football,tennis
```
(will list “active” football and tennis events), similar as taking the union of the results of

```
/football
/tennis
```


### Basic multiple (last level, two participants)
```
/football/all/all/[[[chelsea,arsenal]]]
```
(will list “active” football events for either chelsea or arsenal in any event region and league), similar as taking
the union of the results of

```
/football/all/all/chelsea
/football/all/all/arsenal
```


### Multiple (not last level. two branches)
```
/football,tennis/all/all/[[[arsenal]]],[[[borg__bjorn]]]
```
(will list “active” events for arsenal in football or borg__bjorn in tennis, but not arsenal in tennis or borg__bjorn
in football), similar as taking the union of the results of

```
/football/all/all/arsenal
/tennis/all/all/borg__bjorn
```
The square brackets is used to indicate which parent element it belongs to (arsenal to football and borg__bjorn to tennis).

### Multiple (not last level. two branches) - Implicit all

```
/football,tennis/all/all/[[[arsenal]]]
```
(will list “active” events for arsenal in football or any tennis (not related to arsenal), similar as taking the union
of the results of

```
/football/all/all/arsenal
/tennis/all/all/all (which is same as /tennis)
```
For the tennis, the all-participant is implicit. If parent levels splits up into more branches than covered in lower
level, those children are implicit all.

### Multiple (in two levels, three branches)
```
/football,tennis/all/all/[[[arsenal,chelsea]]],[[[borg__bjorn]]]
```
(will list “active” events for arsenal or chelsea in football or borg__bjorn in tennis, but not arsenal nor chelsea in
tennis neither borg__bjorn in football), similar as taking the union of the results of

```
/football/all/all/arsenal
/football/all/all/chelsea
/tennis/all/all/borg__bjorn
```

### Multiple (in several levels, five branches)

```
/football,tennis/[england,sweden],[atp]/[[premier_league],[allsvenskan,superettan]],[[wimbledon]]/[[[arsenal,chelsea]],[[hammarby]]],[[[borg__bjorn]]]]
```
(will list all “active” events for arsenal or chelsea in premier league, or hammarby in allsvenskan or borg__bjorn in
wimbledon), similar as taking the union of the results of

```
/football/england/premier_league/arsenal
/football/england/premier_league/chelsea
/football/sweden/allsvenska/hammarby
/football/sweden/superettan/all (which is same as /football/sweden/superettan)
/tennis/atp/wimbledon/borg__bjorn
```
The common rule is that for each split up, the number of surrounding square brackets are increased by one on the next
level having a split up.

### Multiple (in several levels, six branches) - Implicit all

```
/football,tennis,bandy/[england,sweden],[atp]/[[premier_league],[allsvenskan,superettan]],[[wimbledon]]/[[[arsenal,chelsea]],[[hammarby]]],[[[borg__bjorn]]],[[[boltic]]]
```
which with explicit all is identical to

```
/football,tennis,bandy/[england,sweden],[atp],[all]/[[premier_league],[allsvenskan,superettan]],[[wimbledon]],[[all]]/[[[arsenal,chelsea]],[[hammarby]]],[[[borg__bjorn]]],[[[boltic]]]
```
(will list same as previous plus consider events which involves boltic bandy games)

```
/bandy/all/all/boltic
```
