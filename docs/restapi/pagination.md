# Pagination

As a rule, the number of items in API response list is limited. In order to get access to all contents the API client should request data by ranges. Range is identified by HTTP query parameters range_start and range_size. Both should be positive integers.

The following rules are applied:

Pagination is only supported for resource of type /betoffer/...
- Minimum allowed value of range size is 1 (one).
- Maximum allowed value of range size is 2000 (two thousand).
- Default value for range_start is 0 (zero).
- Default value for range_size is different for resources.
- If range_size is greater than allowed, then the maximum allowed value is used
- If range_size is less than allowed, then the minimum allowed value is used

If the complete list of requested items fits into the specified range then range data may not be present in the response entity. If the total count is not provided, the hasNext property should be used for determining if any additional results can be fetched. More details on range usage may be provided in resource specifications.

**Example URL:** 
/offering/v2018/kambi/betoffer/group/1000093190.json?range_size=25&range_start=50

Example response
```
"range":{"start":20,"size":10,"total":100}
```

Example response (no total)
```
"range":{"start":20,"size":10,"hasNext":false}
```