# API Access

The Flow Results specification is designed to be useful in both file-based and API-based environments. File-based usage is ideal for archiving and transferring data manually between systems. However, in many production situations, interoperable systems are likely to exchange data via HTTP-based API services. When implementations provide access to Flow Results Packages and Responses via an API, the following standardized endpoints, parameters, and envelope formats shall be used.

## API Authentication

Two methods of authentication are supported for clients accessing Flow Results APIs.  All methods should use HTTPS for security.

### Token-based authentication

Implementations must support token-based authentication, via the "Authorization" HTTP header, using the Token method.  An example of a complete authorization header is:

```
Authorization: Token 0b79bab50daca910b000d4f1a2b675d604257e42
```

Implementations can determine the format of tokens.  The issuance, expiry, and exchange of tokens is left outside the scope of the Flow Results specification.

### HTTPS Basic Auth

Providing additional support for HTTPS Basic Auth is optional, but recommended.

## API Request and Response format

The Flow Results API adheres to the [JSON API](http://jsonapi.org/format/) specification, version 1.0, an open standard for how a client should request that resources be fetched or modified, and how a server should respond to those requests, via JSON.  All API requests and responses defined below reflect the JSON API norms for query parameters and pagination. JSON API also introduces an envelope structure to embed the Flow Results JSON within additional metadata. Adopting JSON API allows API clients to make use of standard [JSON API libraries](http://jsonapi.org/implementations/).

## API Endpoints

Five standard API endpoints are defined for Flow Results servers operating in the Data Aggregator role. Two "push" endpoints are used to send flow results to a system playing the Data Aggregator role.  Three "pull" endpoints are used to access flow results served by a Data Aggregator.

Endpoints are defined relative to a base URL chosen by the implementation, i.e.:

_Base URL_: [https://my.example-flow-results-server/api/v1](https://my.example-flow-results-server/api/v1)

### "Push" endpoints to submit flow results to a Data Aggregator

#### Publish a Package:

This endpoint is used to publish a Package descriptor for the first time to a Data Aggregator.

_URL_: POST \[Base URL\]/flow-results/packages

_Query parameters_: None

_Request body_: The request body shall specify the `type` of `packages`. It shall contain, within the `attributes` parameter, the JSON structure of the Descriptor.

The `id` of the Descriptor can be omitted \(or null\) if the client wants the Data Aggregator \(server\) to assign a new UUID for this package.  If an `id` is provided, the client must ensure that the UUID does not conflict with a Package `id` that already exists on the Data Aggregator; servers must reject requests with `id` conflicts. This is consistent with the [JSON API specification](http://jsonapi.org/format/#crud-creating-client-ids) for client-generated IDs:

A server MAY accept a client-generated ID along with a request to create a resource. An ID MUST be specified with an id key, the value of which MUST be a universally unique identifier. The client SHOULD use a properly generated and formatted UUID as described in RFC 4122 \[RFC4122\]. A server MUST return 403 Forbidden in response to an unsupported request to create a resource with a client-generated ID.

_Request example:_

```
POST [Base URL]/flow-results/packages HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
   "data":{
      "type":"packages",
      "attributes":{
         "profile":"flow-results-package",
         "name":"standard_test_survey",
         "flow-results-specification":"1.0.0-rc1",
         "created":"2015-11-26 02:59:24+00:00",
         "modified":"2017-12-04 15:54:44+00:00",
         "id":null,
         "title":"Standard Test Survey",
         "resources":[
            {
               "path":null,
               "api-data-url":null,
               "mediatype":"application/json",
               "encoding":"utf-8",
               "schema":{
                  "language":"eng",
                  "fields":[
                     {
                        "name":"timestamp",
                        "title":"Timestamp",
                        "type":"datetime"
                     },
                     {
                        "name":"row_id",
                        "title":"Row ID",
                        "type":"string"
                     },
                     {
                        "name":"contact_id",
                        "title":"Contact ID",
                        "type":"string"
                     },
                     {
                        "name":"session_id",
                        "title":"Session ID",
                        "type":"string"
                     },
                     {
                        "name":"question_id",
                        "title":"Question ID",
                        "type":"string"
                     },
                     {
                        "name":"response_id",
                        "title":"Response ID",
                        "type":"any"
                     },
                     {
                        "name":"response_metadata",
                        "title":"Response Metadata",
                        "type":"object"
                     }
                  ],
                  "questions":{
                     "1448506769745_42":{
                        "type":"select_one",
                        "label":"Are you a woman or a man?",
                        "type_options":{
                           "choices":[
                              "Woman",
                              "Man",
                              "Other"
                           ]
                        }
                     },
                     "1448506773018_89":{
                        "type":"numeric",
                        "label":"How old are you? Please enter your age in years.",
                        "type_options":{
                           "range":[
                              -99,
                              99
                           ]
                        }
                     },
                     "1448506774930_30":{
                        "type":"open",
                        "label":"What was the best thing that happened to you today?",
                        "type_options":{

                        }
                     }
                  }
               }
            }
         ]
      }
   }
}
```

_Response_:

The response from the server must adhere to the [JSON API specification](http://jsonapi.org/format/#crud-creating-responses) for POST responses.

_Response example:_

```
HTTP/1.1 201 Created
Location: https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa
Content-Type: application/vnd.api+json

{
   "data":{
      "type":"packages",
      "id":"0c364ee1-0305-42ad-9fc9-2ec5a80c55fa",
      "attributes":{
         "profile":"flow-results-package",
         "name":"standard_test_survey",
         "flow-results-specification":"1.0.0-rc1",
         "created":"2015-11-26 02:59:24+00:00",
         "modified":"2017-12-04 15:54:44+00:00",
         "id":"0c364ee1-0305-42ad-9fc9-2ec5a80c55fa",
         "title":"Standard Test Survey",
         "resources":[
            {
               "path":null,
               "api-data-url":"https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa/responses",
               "mediatype":"application/json",
               "encoding":"utf-8",
               "schema":{
                  "language":"eng",
                  "fields":[
                     {
                        "name":"timestamp",
                        "title":"Timestamp",
                        "type":"datetime"
                     },
                     {
                        "name":"row_id",
                        "title":"Row ID",
                        "type":"string"
                     },
                     {
                        "name":"contact_id",
                        "title":"Contact ID",
                        "type":"string"
                     },
                     {
                        "name":"session_id",
                        "title":"Session ID",
                        "type":"string"
                     },
                     {
                        "name":"question_id",
                        "title":"Question ID",
                        "type":"string"
                     },
                     {
                        "name":"response_id",
                        "title":"Response ID",
                        "type":"any"
                     },
                     {
                        "name":"response_metadata",
                        "title":"Response Metadata",
                        "type":"object"
                     }
                  ],
                  "questions":{
                     "1448506769745_42":{
                        "type":"select_one",
                        "label":"Are you a woman or a man?",
                        "type_options":{
                           "choices":[
                              "Woman",
                              "Man",
                              "Other"
                           ]
                        }
                     },
                     "1448506773018_89":{
                        "type":"numeric",
                        "label":"How old are you? Please enter your age in years.",
                        "type_options":{
                           "range":[
                              -99,
                              99
                           ]
                        }
                     },
                     "1448506774930_30":{
                        "type":"open",
                        "label":"What was the best thing that happened to you today?",
                        "type_options":{

                        }
                     }
                  }
               }
            }
         ]
      },
      "links":{
         "self":"https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa"
      }
   }
}
```

#### Publish Responses to a Package:

This endpoint is used to publish a collection of Responses for a Package to a Data Aggregator. The POST request can be used multiple times to publish additional responses in batches.  Clients should use batching whenever appropriate instead of posting responses individually.

_URL_: POST \[Base URL\]/flow-results/packages/\[id\]/responses

_Query parameters_: None

_Request body_: The request body shall specify the `type` of `responses`. It shall contain, within the `attributes` parameter, a single `responses` attribute, containing the standard Flow Results row array.

_Request example:_

```
POST [Base URL]/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa/responses HTTP/1.1
Content-Type: application/vnd.api+json
Accept: application/vnd.api+json

{
   "data":{
      "type":"responses",
      "id":"0c364ee1-0305-42ad-9fc9-2ec5a80c55fa",
      "attributes":{
         "responses":[
            [
               "2015-11-26 04:33:26",
               "11393115",
               "10825354",
               "47029339",
               "1448506769745_42",
               "Man",
               {}
            ],
            [
               "2015-11-26 04:33:31",
               "11393119",
               "10825354",
               "47029339",
               "1448506773018_89",
               "30.0000",
               {}
            ],
            [
               "2015-11-26 04:33:35",
               "11393126",
               "10825354",
               "47029339",
               "1448506774930_30",
               "https://go.votomobile.org/audiofiles/download//original",
               {
                  "type":"audio",
                  "format":"audio/wav"
               }
            ],
            [
               "2015-11-26 04:34:07",
               "11393169",
               "10825354",
               "47029339",
               "1448506769745_42",
               "Woman",
               {}
            ],
            [
               "2015-11-26 04:34:13",
               "11393172",
               "10825354",
               "47029339",
               "1448506773018_89",
               "40.0000",
               {}
            ]
         ]
      }
   }
}
```

_Response body_: The response from the server must adhere to the [JSON API specification](http://jsonapi.org/format/#crud-creating-responses) for POST responses.  Additionally, servers should make use of the `204 No Content` response mechanism to report acceptance to clients without sending back long documents.

_Response example:_

```
HTTP/1.1 204 No Content
Content-Type: application/vnd.api+json
```

### "Get" endpoints to retrive flow results from a Data Aggregator

#### List all Flow Results Packages

This endpoint is used to request a list of the Flow Results Packages available on the server for the authorized user.

_URL_: GET \[Base URL\]/flow-results/packages

_Query parameters_: None

_Request body_: None

_Request example:_

```
GET [Base URL]/flow-results/packages HTTP/1.1
Accept: application/vnd.api+json
```

_Response body_: The response from the server must adhere to the [JSON API specification](http://jsonapi.org/format/#fetching-resources-responses) for fetching a collection of resources. The `data` array includes the list of package resources. Implementations may decide what summary information they want to publish in the `attributes` for each package.

_Response example:_

```
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
    "links": {
        "self": "https://go.votomobile.org/flow-results/packages?page%5Bsize%5D=100",
        "next": null,
        "previous": null
    },
    "data": [
        {
            "type": "packages",
            "id": "0c364ee1-0305-42ad-9fc9-2ec5a80c55fa",
            "attributes": {
                "title": "Standard Test Survey",
                "name": "standard_test_survey",
                "created": "2015-11-26 02:59:24+00:00",
                "modified": "2017-12-04 15:54:44+00:00"
            }
        },
        {
            "type": "packages",
            "id": "d8d0275f-e108-4528-a259-d738bb626143",
            "attributes": {
                "title": "World Malaria Day Outbound Message",
                "name": "world_malaria_day_outbound_message",
                "created": "2015-05-19 15:59:25+00:00",
                "modified": "2015-06-01 18:57:21+00:00"
            }
        },
        {
            "type": "packages",
            "id": "d24ba50d-e3bf-4eea-b319-ddf8979979d1",
            "attributes": {
                "title": "Example Quotas Survey",
                "name": "example_quotas_survey",
                "created": "2015-12-04 05:45:51+00:00",
                "modified": "2017-11-24 19:58:43+00:00"
            }
        },
        {
            "type": "packages",
            "id": "ba4b5e9e-bb1f-4e92-bfd4-95868ca48b87",
            "attributes": {
                "title": "International Women's Day",
                "name": "international_womens_day",
                "created": "2017-03-08 13:57:15+00:00",
                "modified": "2017-03-09 02:11:31+00:00"
            }
        }
    ]
}
```

#### Get the Descriptor of a Package

This endpoint is used to request the Descriptor of a Flow Results Package

_URL_: GET \[Base URL\]/flow-results/packages/\[id\]

_Query parameters_: None

_Request body_: None

_Request example:_

```
GET [Base URL]/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa HTTP/1.1
Accept: application/vnd.api+json
```

_Response body_: The response from the server must adhere to the [JSON API specification](http://jsonapi.org/format/#fetching-resources-responses) for fetching a resource.

_Response example:_

```
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
   "links":{
      "self":"https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa"
   },
   "data":{
      "type":"packages",
      "id":"0c364ee1-0305-42ad-9fc9-2ec5a80c55fa",
      "attributes":{
         "profile":"flow-results-package",
         "name":"standard_test_survey",
         "flow-results-specification":"1.0.0-rc1",
         "created":"2015-11-26 02:59:24+00:00",
         "modified":"2017-12-04 15:54:44+00:00",
         "id":"0c364ee1-0305-42ad-9fc9-2ec5a80c55fa",
         "title":"Standard Test Survey",
         "resources":[
            {
               "path":null,
               "api-data-url":"https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa/responses",
               "mediatype":"application/json",
               "encoding":"utf-8",
               "schema":{
                  "language":"eng",
                  "fields":[
                     {
                        "name":"timestamp",
                        "title":"Timestamp",
                        "type":"datetime"
                     },
                     {
                        "name":"row_id",
                        "title":"Row ID",
                        "type":"string"
                     },
                     {
                        "name":"contact_id",
                        "title":"Contact ID",
                        "type":"string"
                     },
                     {
                        "name":"session_id",
                        "title":"Session ID",
                        "type":"string"
                     },
                     {
                        "name":"question_id",
                        "title":"Question ID",
                        "type":"string"
                     },
                     {
                        "name":"response_id",
                        "title":"Response ID",
                        "type":"any"
                     },
                     {
                        "name":"response_metadata",
                        "title":"Response Metadata",
                        "type":"object"
                     }
                  ],
                  "questions":{
                     "1448506769745_42":{
                        "type":"select_one",
                        "label":"Are you a woman or a man?",
                        "type_options":{
                           "choices":[
                              "Woman",
                              "Man",
                              "Other"
                           ]
                        }
                     },
                     "1448506773018_89":{
                        "type":"numeric",
                        "label":"How old are you? Please enter your age in years.",
                        "type_options":{
                           "range":[
                              -99,
                              99
                           ]
                        }
                     },
                     "1448506774930_30":{
                        "type":"open",
                        "label":"What was the best thing that happened to you today?",
                        "type_options":{

                        }
                     }
                  }
               }
            }
         ]
      }
   },
   "relationships":{
      "responses":{
         "links":{
            "related":"https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa/responses"
         }
      }
   }
}
```

#### Get Responses for a Package

This endpoint is used to request some or all Responses on a Flow Results Package. It supports pagination for large data sets, and a minimum set of filter parameters.  Implementations may add support for additional filters.

_URL_: GET \[Base URL\]/flow-results/packages/\[id\]/responses

_Query parameters_:

* `filter[max-version]`: Only included Responses recorded on versions of this Package less recent than and including this version. \(This is a timestamp corresponding to the `modified` field of a Descriptor.\)
* `filter[min-version]`: Only include Responses recorded on versions of this Package more recent than and including this version. \(This is a timestamp corresponding to the `modified` field of a Descriptor.\)
* `filter[start-timestamp]`: Only show Responses that were recorded after this timestamp \(exclusive\). \(This is a timestamp in the format of RFC 3339, section 5.6, `date-time`.\)
* `filter[end-timestamp]`: Only show Responses that were recorded before and on this timestamp \(inclusive\). \(This is a timestamp in the format of RFC 3339, section 5.6, `date-time`.\)
* `page[size]`: The requested number of responses per pagination page
* `page[afterCursor]`: The response `row_id` to requests responses after this id, when paginating forward
* `page[beforeCursor]`: The response `row_id` to request responses prior to this id, when paginating in reverse

_Request body_: None

_Request example:_

```
GET [Base URL]/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa/responses?page[size]=5 HTTP/1.1
Accept: application/vnd.api+json
```

_Response body_: The response from the server must adhere to the [JSON API specification](http://jsonapi.org/format/#fetching-resources-responses) for fetching a resource. The `type` of the resource must be `responses`.  The `attributes` field contains a single attribute `responses`, containing the results row array in Flow Results format.

_Response example:_

```
HTTP/1.1 200 OK
Content-Type: application/vnd.api+json

{
    "data": {
        "type": "responses",
        "id": "0c364ee1-0305-42ad-9fc9-2ec5a80c55fa",
        "attributes": {
            "responses": [
                [
                    "2015-11-26 04:33:26",
                    "11393115",
                    "10825354",
                    "47029339",
                    "1448506769745_42",
                    "Man",
                    {}
                ],
                [
                    "2015-11-26 04:33:31",
                    "11393119",
                    "10825354",
                    "47029339",
                    "1448506773018_89",
                    "30.0000",
                    {}
                ],
                [
                    "2015-11-26 04:33:35",
                    "11393126",
                    "10825354",
                    "47029339",
                    "1448506774930_30",
                    "https://example-test-server.com/audiofiles/download/1448506774930_30/original",
                    {
                        "type": "audio",
                        "format": "audio/wav"
                    }
                ],
                [
                    "2015-11-26 04:34:07",
                    "11393169",
                    "10825354",
                    "47029339",
                    "1448506769745_42",
                    "Woman",
                    {}
                ],
                [
                    "2015-11-26 04:34:13",
                    "11393172",
                    "10825354",
                    "47029339",
                    "1448506773018_89",
                    "40.0000",
                    {}
                ]
            ]
        },
        "relationships": {
            "descriptor": {
                "links": {
                    "self": "https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa"
                }
            },
            "links": {
                "self": "https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa/responses?page%5Bsize%5D=5",
                "next": "https://go.votomobile.org/flow-results/packages/0c364ee1-0305-42ad-9fc9-2ec5a80c55fa/responses?page%5Bsize%5D=5&page%5BafterCursor%5D=11393172",
                "previous": null
            }
        }
    }
}
```



