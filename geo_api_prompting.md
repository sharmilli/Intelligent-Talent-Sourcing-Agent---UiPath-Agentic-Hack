# Geographical Data API Prompting System

## Overview
This system provides structured prompting for querying geographical data including countries, cities, towns, and their coordinates (latitude/longitude) worldwide.

## API Query Formats

### 1. Basic Location Queries

#### Country Information
```
GET /countries/{country_name}
Query: "Get country information for {country_name}"
Response: Country name, capital, ISO codes, continent, population, area, coordinates
```

#### City Information
```
GET /cities/{city_name}
Query: "Get city information for {city_name}"
Response: City name, country, state/province, population, coordinates, timezone
```

#### Town Information
```
GET /towns/{town_name}
Query: "Get town information for {town_name}"
Response: Town name, country, state/province, population, coordinates, administrative level
```

### 2. Coordinate-Based Queries

#### Exact Coordinates
```
GET /coordinates/{latitude}/{longitude}
Query: "Get location at coordinates {latitude}, {longitude}"
Response: Address, city, country, administrative divisions
```

#### Reverse Geocoding
```
GET /reverse-geocode
Query: "What location is at {latitude}, {longitude}?"
Response: Full address breakdown with administrative levels
```

### 3. Search and Filter Queries

#### Search by Name Pattern
```
GET /search?name={pattern}&type={location_type}
Query: "Find all {cities/towns/countries} with names containing '{pattern}'"
Response: List of matching locations with coordinates
```

#### Geographic Region Queries
```
GET /region/{continent}/countries
Query: "List all countries in {continent} with their capitals and coordinates"
Response: Country list with capitals and coordinate data
```

#### Proximity Searches
```
GET /nearby?lat={lat}&lon={lon}&radius={km}&type={type}
Query: "Find all {cities/towns} within {radius} km of {latitude}, {longitude}"
Response: List of nearby locations with distances and coordinates
```

## Structured Prompt Templates

### Template 1: Single Location Query
```
QUERY: Get complete geographical data for [LOCATION_NAME]
REQUIRED_FIELDS: 
- Name
- Country (if city/town)
- Latitude
- Longitude
- Population
- Administrative level
- Timezone
RESPONSE_FORMAT: JSON structure with nested location hierarchy
```

### Template 2: Coordinate Query
```
QUERY: Identify location at coordinates [LATITUDE], [LONGITUDE]
REQUIRED_FIELDS:
- Exact address
- City/Town name
- Administrative divisions (state, province, region)
- Country
- Postal code (if available)
RESPONSE_FORMAT: Hierarchical address structure
```

### Template 3: Multi-Location Query
```
QUERY: List all [LOCATION_TYPE] in [REGION/COUNTRY] with coordinates
FILTERS:
- Population range: [MIN] to [MAX]
- Administrative level: [LEVEL]
- Coordinate bounds: [BOUNDS]
SORT_BY: [population/name/latitude/longitude]
RESPONSE_FORMAT: Array of location objects
```

### Template 4: Geographic Calculations
```
QUERY: Calculate [CALCULATION_TYPE] between locations
PARAMETERS:
- Origin: [LOCATION_1] or [LAT1, LON1]
- Destination: [LOCATION_2] or [LAT2, LON2]
- Unit: [kilometers/miles/nautical_miles]
CALCULATIONS: [distance/bearing/midpoint/timezone_difference]
```

## Response Structure Examples

### Standard Location Response
```json
{
  "name": "Paris",
  "type": "city",
  "country": {
    "name": "France",
    "iso_code": "FR",
    "continent": "Europe"
  },
  "coordinates": {
    "latitude": 48.8566,
    "longitude": 2.3522
  },
  "administrative": {
    "region": "Île-de-France",
    "department": "Paris"
  },
  "population": 2161000,
  "timezone": "Europe/Paris",
  "elevation": 35
}
```

### Coordinate Search Response
```json
{
  "coordinates": {
    "latitude": 40.7128,
    "longitude": -74.0060
  },
  "location": {
    "address": "New York, NY, USA",
    "city": "New York",
    "state": "New York",
    "country": "United States",
    "postal_code": "10001"
  },
  "administrative_levels": [
    {"level": 1, "name": "United States"},
    {"level": 2, "name": "New York"},
    {"level": 3, "name": "New York County"}
  ]
}
```

## Advanced Query Patterns

### Batch Queries
```
QUERY: Get coordinates for multiple locations
INPUT: ["London, UK", "Tokyo, Japan", "Sydney, Australia"]
OUTPUT: Array of location objects with coordinates
```

### Conditional Queries
```
QUERY: Find cities WHERE population > 1000000 AND country = "Germany"
FIELDS: name, coordinates, population
ORDER_BY: population DESC
```

### Hierarchical Queries
```
QUERY: Get administrative hierarchy for location
INPUT: Specific coordinates or location name
OUTPUT: Country → State/Province → County/Region → City → District
```

## Query Parameters Reference

### Location Types
- `country`: Sovereign nations
- `city`: Major urban centers
- `town`: Smaller urban areas
- `village`: Rural settlements
- `administrative`: Administrative divisions

### Coordinate Formats
- Decimal degrees: `40.7128, -74.0060`
- Degrees/minutes/seconds: `40°42'46"N, 74°0'22"W`
- UTM coordinates: `Zone 18T 583960 4507523`

### Filter Parameters
- `population_min`: Minimum population
- `population_max`: Maximum population
- `bbox`: Bounding box coordinates
- `country_code`: ISO country codes
- `timezone`: Timezone identifier
- `language`: Primary language

## Error Handling Patterns

### Location Not Found
```
QUERY: "Get information for non-existent location"
RESPONSE: "Location not found. Suggestions: [similar_names]"
```

### Ambiguous Location
```
QUERY: "Get coordinates for Springfield"
RESPONSE: "Multiple locations found. Please specify: [list_of_options]"
```

### Invalid Coordinates
```
QUERY: "Get location at invalid coordinates"
RESPONSE: "Invalid coordinates. Valid range: Latitude [-90, 90], Longitude [-180, 180]"
```

## Usage Examples

### Example 1: City Lookup
```
Human: Get coordinates for Tokyo, Japan
System: Tokyo, Japan is located at coordinates 35.6762°N, 139.6503°E. It's the capital city with a metropolitan population of approximately 37.4 million people, located in the Kantō region on Honshu island.
```

### Example 2: Reverse Geocoding
```
Human: What location is at coordinates 51.5074, -0.1278?
System: These coordinates correspond to London, England, United Kingdom. Specifically, this is in the City of Westminster, near the Thames River in central London.
```

### Example 3: Regional Query
```
Human: List all capital cities in South America with their coordinates
System: [Returns structured list of all South American capitals with precise coordinates, countries, and populations]
```

This API-style prompting system provides comprehensive coverage for geographical queries while maintaining consistent structure and format for reliable data retrieval.