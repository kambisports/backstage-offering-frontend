# Localization and Markets

@[toc]

## Localization
Throughout the API most resources return responses which are translated according to locale given by the query parameter `lang`.

The value of the `lang` parameter should conform to either of the following:

- Language only (ISO-639 format). e.g. lang=en. The origin country is assumed, 
e.g. lang=en is treated like en_GB
- Language and country (ISO-639_ISO-3166), e.g. lang=de_AT

Parameter is case insensitive, i.e. De_aT is considered as valid and treated as 
de_AT. 

### Supported Locales

|Language                 | Locale|
|-------------------------|-------|
|English (United Kingdom) |en_GB|
|English (Australia)      |en_AU|
|English (Ireland)        |en_IE|
|English (US)             |en_US|
|Bulgarian (Bulgaria)     |bg_BG|
|Croatian (Croatia)       |hr_HR|
|Czech (Czech Republic)   |cs_CZ|
|Danish (Denmark)         |da_DK|
|Dutch (Netherlands)      |nl_NL|
|Dutch (Belgium)          |nl_BE|
|Estonian (Estonia)       |et_EE|
|Finnish (Finland)        |fi_FI|
|French (France)          |fr_FR|
|French (Belgium)         |fr_BE|
|French (Switzerland)     |fr_CH|
|Georgian (Georgia)       |ka_GE|
|German (Germany)         |de_DE|
|German (Austria)         |de_AT|
|German (Switzerland)     |de_CH|
|Greek (Greece)           |el_GR|
|Hungarian (Hungary)      |hu_HU|
|Italian (Italy)          |it_IT|
|Latvian (Latvia)         |lv_LV|
|Lithuanian (Lithuania)   |lt_LT|
|Norwegian (Norway)       |no_NO|
|Polish (Poland)          |pl_PL|
|Portuguese (Portugal)    |pt_PT|
|Portuguese (Brazil)      |pt_BR|
|Romanian (Romania)       |ro_RO|
|Russian (Russia)         |ru_RU|
|Spanish (Spain)          |es_ES|
|Spanish (Argentina)      |es_AR|
|Spanish (Chile)          |es_CL|
|Spanish (Colombia)       |es_CO|
|Spanish (Mexico)         |es_MX|
|Spanish (Peru)           |es_PE|
|Swedish (Sweden)         |sv_SE|
|Turkish (Turkey)         |tr_TR|

### Default and Unsupported Locales
If no lang parameter is given the default is en_GB.

If submitted language is invalid the API responds with status 400 and list of 
supported languages is enclosed into response entity.

### name and englishName
Many entities like event, bet offer etc provides the two fields `name` and `englishName` in the API response.
 
`name` is a localized formatted label of the entity and should be displayed to end-users.
 
The other field `englishName` is a raw representation in english. Same value regardless of requested offering, language
or market. It is exposed in the Offering API to simplify tracking of an entity with a text label (instead of numeric Id)
in Google Analytics etc. The raw representation is also what is displayed within the Kambi backend tools for an entity.
It is not meant to be displayed for the end-user.

### Translation information about event and event group
Besides the localization within the regular Offering resources, there is a specific resource /offering/v2018/kambi/translation,
which returns translated names of specified events and event groups.

## Geographical Markets
The query parameter `market` is used to specify a country OR a geographical region within a country. The market affects different things like:

-  Price differentiation
-  The availability of video streaming on live events. 
-  Sorting of events and event groups configured in Presentation Manager
-  Popular (Trending) Bet Offers

The parameter format should conform to one of the following:
- Two character country string as specified in the ISO-3166 standard
- 4-5 character country and region string as specified in the ISO-3166-2 standard.

Some features are only configured on country level, and will then be unaffected if market contains region.

## Default and Unsupported Markets
If market parameter is given as an invalid country (ISO-3166), it is ignored and Kambi default configuration is used.

If market parameter is given as an invalid country and region (ISO-3166-2), the API responds with status 400 and list of
supported values for market parameter is enclosed into response entity.
