# Introduction

The aim of the API is to expose Kambi Sportsbook Offering functionality to outside 
world in a RESTful manner. HTTP and various output data formats make the API 
a real cross-platform interface between Sportsbook and its consumers.

### Domain

The high level view of betting domain model can be represented by four entities:

    Event Group
        |
        +--- Event (matches & competitions)
               |
               +--- Bet offer
                          |
                          +--- Outcome (with odds)


- [Event Group](#/docs/domain/event_group)
- [Event](#/docs/domain/event)
- [Bet offer](#/docs/domain/betoffer)
- [Outcome](#/docs/domain/outcome)

### Common conventions

HTTPS is used for transport protocol. The API reuses the features of HTTP (methods, statuses) for communication purposes.
    
In most cases the API follows HTTP standards. Deviations from HTTP standards are documented at relevant API method specifications.
    
### Operations
We use RESTful verbs for all operations and methods.

| Verb | Description |
| ------ | ----------- |
| `GET`  | Used to select one or more items. Success returns 200 status code with json result item(s) in body. |

### Response format
We both accepts JSON format and respond with it for all API call. All entities have a defined schema available in an Open API v3 format. This is available in the [Explorer](#/explorer)

### Authentication
The API needs no authentication. The content of the API is only affected by given offering, market and language.
If the client is an anonymous user or a punter logged in through Player API has no affect on the response of the Offering API.

### Versioning
The version of the REST API is given in the URL to endpoints.
Initial version is "v2018" and is guaranteed to exist for two years from release date.
