# Specification

The Flow Results specification builds on the [Data Package](https://specs.frictionlessdata.io/data-package/) specification. Thus a Flow Results Data Package must be a Data Package and conform to the Data Package specification.

Additionally, a Flow Results data package must conform to these additional requirements:

## Components

A Flow Results data package consists of one [Descriptor](https://specs.frictionlessdata.io/data-package/#descriptor), which contains exactly one [Resource](https://specs.frictionlessdata.io/data-resource/) describing the interaction results.  

## Descriptor

The Descriptor JSON object must contain the following required metadata properties:

<table>
  <tr>
    <td>Property</td>
    <td>Description</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>`profile`</td>
    <td>Indicates this package is a Flow Results data package. Must be in the format of the example.</td>
    <td>'flow-results-package'</td>
  </tr>
  <tr>
    <td>`flow_results_specification_version`</td>
    <td>Indicates the version of this specification the package is compliant with. The Flow Results specification adheres to semantic versioning.</td>
    <td>'1.0.0-rc1'</td>
  </tr>
  <tr>
    <td>`created`</td>
    <td>The timestamp for when this package was created/published. This must be in the format of RFC 3339, section 5.6, "date-time".</td>
    <td>'2017-06-30 15:35:27+00:00'</td>
  </tr>
  <tr>
    <td>`modified`</td>
    <td>A version control indicator for the package. Timestamps are used to indicate different versions of a package's schema. This must be in the format of RFC 3339, section 5.6, "date-time".
      <br><br>Limited changes are allowed across versions of the same package (i.e.: different versions with the same `id`.). Specifically, new versions of the same package may add additional `questions` within the schema; however, questions may not be removed, and the metadata for existing questions may not be changed. For more information on version support in Flow Results packages, see [Results Versioning](#results-versioning).
      <br><br>If this is the original version of the package, `created` and `modified` will be the same.
    </td>
    <td>'2017-06-30 15:38:05+00:00'</td>
  </tr>
  <tr>
    <td>`id`</td>
    <td>
    A property reserved for globally unique identifiers. The Data Packages specification supports any identifiers that are unique including UUIDs and DOIs.  <br>

Flow Results packages impose the additional requirement that `id`'s are Version 4 UUIDs (RFC 4122).

The `id` is a required property, except when a Data Collector uses the Flow Results API to publish a new package to a Data Aggregator. In this case, the `id` may optionally be omitted, and will be assigned by the Data Aggregator and returned. For more information, see the section on [API Usage](#api-usage). </td>
    <td>"b03ec84-77fd-4270-813b-0c698943f7ce"</td>
  </tr>
</table>


The following metadata properties are recommended, consistent with the Data Packages specification:

<table>
  <tr>
    <td>Property</td>
    <td>Description</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `name`</td>
    <td>A short url-usable (and preferably human-readable) name of the package. This MUST be lower-case and contain only alphanumeric characters along with ".", "_" or "-" characters. It will function as a unique identifier and therefore SHOULD be unique in relation to any registry in which this package will be deposited (and preferably globally unique).</td>
    <td>"flow-results-demo-package"</td>
  </tr>
  <tr>
    <td>
    `title`</td>
    <td>A string providing a title or one-sentence description for this package.  This provides suggested human-readable text to display as a label for results/visualization of the entire package.</td>
    <td>"March 2017 Malaria Protection Survey"</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


## Resource

The Resource contains the interaction results.  The Resource must conform to the [Data Package Resource](https://specs.frictionlessdata.io/data-resource/) specification. Additionally:

Inline data (data in JSON format within the Descriptor) must not be used. This means that a `path` must be provided for the Resource. The Resource path shall point to a file reference (for packages stored on disk) or a URL.

The `access_method` of the Resource is an optional parameter, and can be either `api` or `file`. If the access method is `api`, it indicates the resource can be queried using the [API Usage]((#api-usage) specification, with support for pagination and filtering. If the access method is `file`, it indicates all responses are available in a static JSON file. The default if this parameter is not provided is `file`.

The `schema` property of the resource must be provided inline, and must not use an external schema file or URL.

The `schema` property must contain a `fields` object describing the 6 columns within the Resource data. These fields are common to all Flow Results Packages, but are provided here for compatibility with software designed to dynamically read Tabular Data Resources:

```
{
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
      "name":"question_id",
      "title":"Question ID",
      "type":"string"
    },
    {
      "name":"response",
      "title":"Response",
      "type":"any"
    },
    {
      "name":"response_metadata",
      "title":"Response Metadata",
      "type":"object"
    }
  ]
}
```

The `schema` property must additionally contain a `questions` object describing metadata for all the Questions pertaining to Responses in this package.  The object identifier (e.g.: 'ae54d3') of questions in this object connects to the Question ID found in each Response row:

```
{
  "questions":{
    "ae54d3":{
      "type":"multiple_choice",
      "label":"Are you male or female?",
      "type_options":{
        "choices":[
          "male",
          "female",
          "not identified"
        ]
      }
    },
    "ae54d7":{
      "type":"multiple_choice",
      "label":"Favorite ice cream flavor?",
      "type_options":{
        "choices":[
          "chocolate",
          "vanilla",
          "strawberry"
        ]
      }
    },
    "ae54d8":{
      "type":"numeric",
      "label":"How much do you weigh, in lbs?",
      "type_options":{
        "range":[1, 350]
      }
    },
    "ae54da":{
      "type":"open",
      "label":"How are you feeling today?",
      "type_options":{

      }
    }
  }
}
```

The following properties are required for each question:

<table>
  <tr>
    <td>Property</td>
    <td>Description</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `type`
    </td>
    <td>Describes the semantic type of the Question, which must be from the following list:
multiple_choice_one
multiple_choice_many
numeric
open
text
image
video
audio
geo_point
date
time
datetime</td>
    <td>'type':'multiple_choice_one'</td>
  </tr>
  <tr>
    <td>
    `label`
    </td>
    <td>A human-readable label that can be used to present and provide context for this Question/Response.  This is provided in a single default language; localization is left outside the scope of this specification.</td>
    <td>'label':'Are you male or female?'</td>
  </tr>
  <tr>
    <td>
    `type_options`
    </td>
    <td>Dependent on the `type`, an object representing additional metadata for this Question.  Required and optional type_options are listed below under Question Types.</td>
    <td>'choices':['male', 'female']</td>
  </tr>
</table>

The `schema` property may optionally contain a `language` property. If provided, this must be in the form of ISO 639-3, describing the language of the labels in the `questions` object.  Localization of these labels is left outside the scope of the Flow Results specification.

## Resource Data (found at external path)

The Resource file or URL must provide the Response data in JSON "row array" format, as shown in the following example:

```
[
  [ "2017-05-23T13:35:37.119-04:00", 20394823948, 923842093, "ae54d3", "female", {"option_order": ["male","female"]} ],
  [ "2017-05-23T13:35:47.822-04:00", 20394823950, 923842093, "ae54d7", "chocolate", null ]
]
```

The Resource must be valid JSON according to RFC 7159.  No enhancements or constraints are added beyond the JSON specification.

Each row array shall provide exactly 6 elements ("columns") describing a single Response from a Contact. In order, the columns represent:

<table>
  <tr>
    <td>#</td>
    <td>Column</td>
    <td>Description</td>
    <td>Examples</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Timestamp</td>
    <td>The date and time the response was given by the contact. The timestamp must be formatted according to RFC 3339, section 5.6, `date-time`, and must indicate the timezone offset of the timestamp. An example is the following format: `2017-05-23T13:35:37-04:00`.  If the timestamp is in UTC, the timezone offset of +00:00 shall be used, instead of the `Z` extension.
    
    Consistent with RFC 3339, the seconds field may include a decimal point with up to six trailing digits to indicate sub-second precision (e.g. milliseconds or microseconds), such as `2017-05-23T13:35:37.011208-04:00`. Systems are recommended to preserve as much precision as is available in the original timestamp.</td>
    <td>2017-05-23T13:35:37.291-04:00</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Row ID</td>
    <td>A unique value identifying an individual Response within the Flow Results package. The value must be unique within the entire package.  Row IDs may be an integer or a string.   (The purpose of Row IDs is for systems offering paginated access to Responses within a Package.  Although the rows may not be ordered by Row ID, software hosting data at paginated URLs must maintain an internal ordering based on Row IDs, such that it is possible to return the next X rows after a given Row ID.)</td>
    <td>20394823948

'6085f5f2-80a2-423a-9f66-be3b3d777eea'</td>
  </tr>
  <tr>
    <td>3</td>
    <td>Contact ID</td>
    <td>A unique value identifying the Contact that submitted the Response. Contact IDs must be unique within a Flow Results Package, and may provide additional meaning between vendor platforms across Packages. Contact IDs may be an integer or a string.</td>
    <td>923842093

'43979e6c-6b59-4ccf-a260-4361ebbc3264'</td>
  </tr>
  <tr>
    <td>4</td>
    <td>Question ID</td>
    <td>A unique value identifying the Question that this Response is for.  This connects Response rows to the Question metadata in the Descriptor.</td>
    <td>'ae54d3'</td>
  </tr>
  <tr>
    <td>5</td>
    <td>Response</td>
    <td>The actual value of the Response provided by the Contact.  The format of the Response is determined by nature of the Question (see below).</td>
    <td>'female'</td>
  </tr>
  <tr>
    <td>6</td>
    <td>Response Metadata</td>
    <td>For any Question type, there might be additional metadata describing the Response which varies for each row. This metadata might be required or optional, depending on the Question type.  For example, for a multiple choice question, an optional metadata property is the `choice_order` that the multiple choice options were presented in.</td>
    <td>{'choice_order': ['male', 'female'] }</td>
  </tr>
</table>

## Results Versioning

A common occurrence for users is to make minor changes to an underlying flow that has already started collecting data, and to desire for data collected under new and old versions to be reported/aggregated together.  (Examples of these minor changes include adding a new question to a flow, or removing a question.)  The Flow Results specification provides vendor-optional support for limited changes to flow versions. Implementations may choose to support this functionality or not.

### Option 1: No version aggregation under a Package `id`; each change to a flow creates a new package

Implementations may choose this approach if they do not want to implement any aggregation of responses across multiple versions of a flow, and prefer to leave this aggregation as the responsibility of client software.  In this approach, any changes to the schema of a flow (e.g.: adding, removing, or changing questions) would create a new Package with a new independent `id`.  The implementation would serve separate results for different package `id`s.  Client software or external services could examine the Descriptor of each Package and determine, with additional user information, how to aggregate the responses together.

### Option 2: Limited changes supported under a Package `id`; changes to a flow create new versions under the same `id`

Implementations that wish to provide aggregation of responses across multiple versions of a flow may serve results from multiple versions under a single package `id`, according to the following constraints. Specifically, newer versions of the same package `id` may add additional `questions` within the schema; however, questions may not be removed, and the metadata for existing questions may not be changed. This implies that if a newer version of a flow removes a question from a previous version, the old question will continue to be listed in the schema for the new version. (This ensures that the schema of the most recent version contains a complete set of questions describing all responses in the aggregated resource data, including responses collected under older verions.)  The `modified` timestamp is used as a version control indicator for the Package.

In this case, the response data includes responses collected under multiple versions. API access may implement the filter parameters `min-version` and `max-version` to allow clients to selectively retrieve responses from specific versions.  (If a client has cached a version of the schema from a Package descriptor, it is recommended to supply the Package's `modified` descriptor as the `max-version` when querying the API for responses, to ensure it does not receive responses from newer versions without a corresponding `question` in the cached schema.)

For changes to flows that go beyond the restrictions above, new Packages with independent `id`s are required; external clients are responsible for more advanced forms of aggregation across versions of flows.

## Question Types

The following Question Types describe the nature of possible Responses. This section lists the required and optional parameters within the `schema` metadata, and within the Response Metadata for each row:

### select_one

Represents a selection of one choice from a set of discrete choices.  (This is a classic multiple-choice question.)

#### Response format

The Response must be a string; it must be one from the set of `choices`.

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>choices</td>
    <td>Yes</td>
    <td>Array of choices presented to the Contact</td>
    <td>{'choices': ['male', 'female'] }</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>choice_order</td>
    <td>Recommended</td>
    <td>When choices might be presented in random order across Contacts, should indicate the order the choices were presented in.</td>
    <td>{"choice_order": ["female", "male"] }</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### select_many

Represents a selection of one or more choices from a set of discrete choices. (This is a multiple-choice question where the Contact can choose more than one option.)

#### Response format

The Response must be an array of strings, one for each choice selected by the Contact. Each string must be one from the set of `choices`:

```
["education", "roads"]
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `choices`
    </td>
    <td>Yes</td>
    <td>Array of choices presented to the Contact</td>
    <td>{'choices': ['roads', 'healthcare', 'education', 'jobs'] }</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `choice_order`
    </td>
    <td>Recommended</td>
    <td>When choices might be presented in random order across Contacts, should indicate the order the choices were presented in.</td>
    <td>{"choice_order": ["healthcare", "education", "jobs", "roads"] }</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### numeric

Represents a numeric response; a measurement of a single number.

#### Response format

An integer or floating-point number:

```
35
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `range`
    </td>
    <td>Optional</td>
    <td>When the responses are to be visualized on a scale, provides the minimum and maximum relevant values of the range.</td>
    <td>{'range':[0,10]}</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### open

Represents a Response that might be in one of several formats. This Question type is useful for representing open-ended Responses to Flows that run over multiple channels (IVR, SMS, social media) and allow the Contact to submit an audio message, image, video, or text response.

#### Response format

Response must be in the format required for the Question type of each row (where `type` is identified within the response metadata.)

#### Type options (type_options)

None used at the`schema` level.  (Refer to the `type_options` within each row.)

#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `type`
    </td>
    <td>Yes</td>
    <td>Must be one of the other supported question types (e.g., `text`, `audio`, `image`, etc.)</td>
    <td>'text'

'image'</td>
  </tr>
  <tr>
    <td>
    `type_options`
    </td>
    <td>Yes</td>
    <td>Includes the schema metadata (normally found in the schema) that would be used for that response row.</td>
    <td>{}

{'format':'png'}</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td>For additional response metadata, refer to the details for each respective question type.</td>
    <td></td>
  </tr>
</table>


### text

Represents any arbitrary text response.

#### Response format

A string:

```
"I am learning this specification."
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `language`
    </td>
    <td>Optional</td>
    <td>The ISO 639-3 code for the language of the response, if known.</td>
    <td>{'language':'eng'}</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### image

Represents a picture submitted by the Contact.

#### Response format

A string with the URL where the image can be retrieved. (TODO: Do we want to support inline image data?)

```
"https://myexampleflowserver.com/resources/image/23429837433.png"
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `format`
    </td>
    <td>Recommended.</td>
    <td>The mime type of the image.  If not provided, the format may be guessed from the extension or the Content-Type header of the resource.</td>
    <td>"image/png"</td>
  </tr>
  <tr>
    <td>`dimensions`</td>
    <td>Recommended</td>
    <td>The pixel dimensions of the image, if known. If provided, this must be an array of integers, `[width, height]`.</td>
    <td>"dimensions": [128, 128]</td>
  </tr>
  <tr>
    <td>`file_size_mb`</td>
    <td>Recommended</td>
    <td>The total file size, if known. If provided, this must be a number in megabytes (MB).</td>
    <td>"file_size_mb": 38.35</td>
  </tr>
</table>


### video

Represents a video submitted by the Contact

#### Response format

A string with the URL where the video can be retrieved. (TODO: Do we want to support inline video data?)

```
"https://myexampleflowserver.com/resources/videos/23429837434.mp4"
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `format`
    </td>
    <td>Recommended</td>
    <td>The mime type of the video.  If not provided, the format may be guessed from the extension or the Content-Type header of the resource.</td>
    <td>"video/mp4"</td>
  </tr>
  <tr>
    <td>
    `language`
    </td>
    <td>Optional</td>
    <td>The ISO 639-3 code for the language of the response, if known.</td>
    <td>{'language':'eng'}</td>
  </tr>
  <tr>
    <td>
    `dimensions`
    </td>
    <td>Recommended</td>
    <td>The pixel dimensions of the video, if known. If provided, this must be an array of integers, `[width, height]`.</td>
    <td>"dimensions": [480, 360]</td>
  </tr>
  <tr>
    <td>
    `file_size_mb`
    </td>
    <td>Recommended</td>
    <td>The total file size, if known. If provided, this must be a number in megabytes (MB).</td>
    <td>"file_size_mb": 38.35</td>
  </tr>
  <tr>
    <td>
    `duration_s`
    </td>
    <td>Recommended</td>
    <td>The duration of the recording, if known. If provided, this must be a number in seconds (s).</td>
    <td>"duration_s": 16.54</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### audio

Represents an audio recording submitted by the Contact

#### Response format

A string with the URL where the audio can be retrieved. (TODO: Do we want to support inline audio data?)

```
"https://myexampleflowserver.com/resources/audio/23429837435.ogg"
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `format`
    </td>
    <td>Recommended.</td>
    <td>The mime type of the audio.  If not provided, the format may be guessed from the extension or the Content-Type header of the resource.</td>
    <td>"audio/wav"</td>
  </tr>
  <tr>
    <td>
    `language`
    </td>
    <td>Optional</td>
    <td>The ISO 639-3 code for the language of the response, if known.</td>
    <td>{'language':'eng'}</td>
  </tr>
  <tr>
    <td>
    `file_size_mb`
    </td>
    <td>Recommended</td>
    <td>The total file size, if known. If provided, this must be a number in megabytes (MB).</td>
    <td>"file_size_mb": 38.35</td>
  </tr>
  <tr>
    <td>
    `duration_s`
    </td>
    <td>Recommended</td>
    <td>The duration of the recording, if known. If provided, this must be a number in seconds (s).</td>
    <td>"duration_s": 16.54</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### geo_point

Represents a geospatial coordinate on the surface of the earth.

#### Response format

Response must be either:

- An array of two floating point numbers with the latitude and longitude: `[lat, long]`
- An array of three floating point numbers: latitude, longitude, elevation (in meters): `[lat, long, elevation]`
- An array of four floating point numbers: latitude, longitude, elevation, and accuracy (in meters): `[lat, long, elevation, accuracy]`

```
[52.0835780,-106.6104880]
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td>
    `address`
    </td>
    <td>Optional</td>
    <td>TODO: is this useful?</td>
    <td>"Plot 41, Kotei Residential Rd, Kotei, Kumasi, Ashanti Region, Ghana"</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### datetime

Represents a timestamp with both date and time

#### Response format

A string containing the date and time in the RFC 3339 `date-time` format with timezone extension:

```
"2017-06-30T13:45:58+05:30"
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### date

Represents a date.  We caution that dates are ambiguous without times and timezone offsets. Interpretation of the date is left to the publishing and consuming platforms. For example, this could be the date of the start of a pregnancy, in an implied local timezone.

#### Response format

A string containing the date in the format:

```
"2017-06-30"
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


### time

Represents a time.  We caution that times are ambiguous without dates and timezone offsets. Interpretation of the date is left to the publishing and consuming platforms. For example, this could be the time of day a Contact would like to receive messages, in an implied local timezone..

#### Response format

A string containing the time in the 24h format:

```
"14:58:35"
```

#### Type options (type_options)

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


#### Response Metadata

<table>
  <tr>
    <td>Object</td>
    <td>Required</td>
    <td>Details</td>
    <td>Example</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
</table>


