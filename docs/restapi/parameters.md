# Global request parameters
## Mandatory parameters
All API resources must specify the following parameter:

### offering
Unique identifier (“kambi”) assigned to the API user.

Example: /offering/v2018/kambi/group.json?

## Optional parameters
These query parameters are optional, and they are supported by many of the resources.

### suppress_response_codes
If this parameter is specified, the API will always return status code 200 in the response, even for the error responses.

Example: /offering/v2018/kambi/group.json?suppress_response_codes=1

### callback
If this parameter is specified, the API embeds or “pads” the JSON object to a JSONP javascript function. Useful for cross domain data requests. Example: /offering/v2018/kambi/group.json?callback=getGroups

### includeParticipants
Defines if participant information shall be included in the response in addition to the default response.

Example: /offering/v2018/kambi/event/group/1000093190.json?includeParticipants=true

### lang
Defines what language should be used for localized texts. See Localization for detailed description.

Example: /offering/v2018/kambi/group.json?lang=sv_SE

### market
Defines the geographical market that the player is registered in.

See Geographical Markets for detailed description.

### range_start
Defines the pagination offset. Default value is 0 (zero).

Example: /offering/v2018/kambi/betoffer/group/1000093190.json?range_start=100

See also: [Pagination](#/docs/restapi/pagination)

### range_size
Defines the maximum number of items in the response. If omitted, the maximum allowed value is used. If specified size is greater than allowed, then the maximum allowed value is used. If specified size is less than allowed, then the minimum allowed value is used. Minimum allowed value is 1 (one). Maximum allowed value is 2000 (two thousand).

Example: /offering/v2018/kambi/betoffer/group/1000093190.json?range_size=25

See also: [Pagination](#/docs/restapi/pagination)