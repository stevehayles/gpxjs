# GPX.JS

Based on [GPXParser.js](https://github.com/Luuka/GPXParser.js), which has been unmaintained, this updated library is intended to bring modern JavaScript features to GPX parsing, including extensions in tracks and fully featured typescript support.

_I'm also open to any improvements or suggestions with the library, so feel free to leave an issue._

## GPX Schema

The schema for GPX, a commonly used gps tracking format, can be found here: [GPX 1.1](https://www.topografix.com/gpx.asp).

# Usage

**This library currently does not include support for non-browser execution.**

Right now, to use this in Node.js without a browser or in something like React Native, use [`xmldom-qsa`](https://www.npmjs.com/package/xmldom-qsa) instead.

See instructions below on [how to use a custom DOM parser](#using-a-custom-dom-parser).

### Install using NPM

`npm i @we-gold/gpxjs`

Then, import the `parseGPX` method:

```js
import { parseGPX } from "@we-gold/gpxjs"

const [parsedFile, error] = parseGPX(myXMLGPXString)

// Or use a try catch to verify
if (error) throw error

const geojson = parsedFile.toGeoJSON()
```

### Include JavaScript File

In an HTML document:

```html
<script src="./src/gpxjs.js"></script>

<script type="module">
	import { parseGPX } from "@we-gold/gpxjs"

	const [parsedFile, error] = parseGPX(myXMLGPXString)

	// Or use a try catch to verify
	if (error) throw error

	const geojson = parsedFile.toGeoJSON()
</script>
```

### Fetching a GPX File

While this feature isn't included, it is fairly simple to fetch a GPX file and format it as a string.

```js
import { parseGPX } from "@we-gold/gpxjs"

fetch("./somefile.gpx")
	.then((response) => {
		if (!response.ok) {
			throw new Error("Failed to fetch the file")
		}
		return response.text()
	})
	.then((data) => {
		const [parsedFile, error] = parseGPX(data)

		// Or use a try catch to verify
		if (error) throw error

		const geojson = parsedFile.toGeoJSON()
	})
```

### Use the Parsed GPX

```js
const totalDistance = gpx.tracks[0].distance.total

const extensions = gpx.tracks[1].extensions
```

### Export to GeoJSON

```js
const geoJSON = parsedGPX.toGeoJSON()
```

### Using a Custom DOM Parser

If working in an environment where a custom DOM Parser is required, you can include it like so:

_Note, this is significantly slower than using the browser parser._

```js
import { parseGPXWithCustomParser } from "@we-gold/gpxjs"
import { DOMParser } from "xmldom-qsa"

const customParseMethod = (txt: string): Document | null => {
	return new DOMParser().parseFromString(txt, "text/xml")
}

const [parsedFile, error] = parseGPXWithCustomParser(
	myXMLGPXString,
	customParseMethod
)
```

# Documentation

These descriptions are adapted from [GPXParser.js](https://github.com/Luuka/GPXParser.js), with minor modifications.

For specific type definition, see [types.ts](./lib/types.ts).

| Property  | Type               | Description                         |
| --------- | ------------------ | ----------------------------------- |
| xml       | XML Document       | XML Document parsed from GPX string |
| metadata  | Metadata object    | File metadata                       |
| waypoints | Array of Waypoints | Array of waypoints                  |
| tracks    | Array of Tracks    | Array of waypoints of tracks        |
| routes    | Array of Routes    | Array of waypoints of routes        |

## Metadata

| Property    | Type        | Description   |
| ----------- | ----------- | ------------- |
| name        | String      | File name     |
| description | String      | Description   |
| link        | Link object | Web address   |
| author      | Float       | Author object |
| time        | String      | Time          |

## Waypoint

| Property    | Type   | Description       |
| ----------- | ------ | ----------------- |
| name        | String | Point name        |
| comment     | String | Comment           |
| description | String | Point description |
| latitude    | Float  | Point latitute    |
| longitude   | Float  | Point longitude   |
| elevation   | Float  | Point elevation   |
| time        | Date   | Point time        |

## Track

| Property    | Type             | Description                           |
| ----------- | ---------------- | ------------------------------------- |
| name        | String           | Point name                            |
| comment     | String           | Comment                               |
| description | String           | Point description                     |
| src         | String           | Source device                         |
| number      | String           | Track identifier                      |
| link        | String           | Link to a web address                 |
| type        | String           | Track type                            |
| points      | Array            | Array of Points                       |
| distance    | Distance Object  | Distance information about the Route  |
| elevation   | Elevation Object | Elevation information about the Route |
| slopes      | Float Array      | Slope of each sub-segment             |

## Route

| Property    | Type             | Description                           |
| ----------- | ---------------- | ------------------------------------- |
| name        | String           | Point name                            |
| comment     | String           | Comment                               |
| description | String           | Point description                     |
| src         | String           | Source device                         |
| number      | String           | Track identifier                      |
| link        | String           | Link to a web address                 |
| type        | String           | Route type                            |
| points      | Array            | Array of Points                       |
| distance    | Distance Object  | Distance information about the Route  |
| elevation   | Elevation Object | Elevation information about the Route |
| slopes      | Float Array      | Slope of each sub-segment             |

## Point

| Property  | Type  | Description     |
| --------- | ----- | --------------- |
| latitude  | Float | Point latitute  |
| longitude | Float | Point longitude |
| elevation | Float | Point elevation |
| time      | Date  | Point time      |

## Distance

| Property   | Type  | Description                                          |
| ---------- | ----- | ---------------------------------------------------- |
| total      | Float | Total distance of the Route/Track                    |
| cumulative | Float | Cumulative distance at each point of the Route/Track |

## Elevation

| Property | Type  | Description                   |
| -------- | ----- | ----------------------------- |
| maximum  | Float | Maximum elevation             |
| minimum  | Float | Minimum elevation             |
| positive | Float | Positive elevation difference |
| negative | Float | Negative elevation difference |
| average  | Float | Average elevation             |

## Author

| Property | Type         | Description                 |
| -------- | ------------ | --------------------------- |
| name     | String       | Author name                 |
| email    | Email object | Email address of the author |
| link     | Link object  | Web address                 |

## Email

| Property | Type   | Description  |
| -------- | ------ | ------------ |
| id       | String | Email id     |
| domain   | String | Email domain |

## Link

| Property | Type   | Description |
| -------- | ------ | ----------- |
| href     | String | Web address |
| text     | String | Link text   |
| type     | String | Link type   |
