# Handling errors

The API attempts to return appropriate HTTP status codes for every request. 
It is also possible to suppress response codes for the REST API. If parameter 
suppress_response_codes presents in a request query parameter, the API responds 
with status 200 even for errors.

Error response body is formatted according to requested format 
(or application/json if the specified format not supported) and contains the 
status code and a descriptive message. For business logic errors the error 
message may be localized.

Example error response:

```json
{
   "error":{
      "status":500,
      "message":"Internal Server Error"
   }
}
```

## Response status codes
| Code | Description |
| ------ | ----------- |
| `200`  | Success. Server accepted the request, processed it and fulfilled the response according to the service specification. |
| `400`  | Bad Request. The request contained invalid data, e.g. a string was submitted instead of a number or an unsupported language was requested. The accompanying error message may specify more details, e.g. “Bet offer ID ABC is not a valid id”. |
| `404`  | Not Found. The URI requested is invalid or the requested resource does not exist. The accompanying error message may specify more details, e.g. “No bet offers found” (e.g. unknown ID) or “invalid resource path” (e.g. misspelled or unknown path). |
| `405`  | Method Not Allowed. The request used a not allowed method, for example POST when only GET is allowed. |
| `406`  |  Not Acceptable. The server cannot produce the response in a format requested by the client agent. The list of supported media types will be enclosed in the response entity. The response entity format is JSON. |
| `500`  | Internal Server Error. Something is broken. An accompanying error message may explain the reason.|
