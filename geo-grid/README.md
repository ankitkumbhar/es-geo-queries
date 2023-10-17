# Geo-grid:
- The query is designed to match the documents that fall inside a bucket of a geogrid aggregation.
- Mainly used for grouping/aggregations.

## Example:
```
PUT  my_grid_locations
{
	“mappings”: {
		“properties”: {
			“location”: {
				“type”: “geo_point”
			}
		}
	}
}

PUT my_grid_locations/_doc/1
{
	“location”: “POINT (4.912350 52.374081)”,
	“city”: “Amsterdam”,
	“name”: “NEMO Science Museum”
}

PUT my_grid_location/_doc/2
{
	“location”: “POINT(4.405200 51.222900)”,
	“city”: “Antwerp”,
	“name”: “Letternehuis”
}

PUT my_grid_locations/_doc/3
{
	“location”: “POINT(2.336389 48.861111)”,
	“city”: “Paris”,
	“name”: “Musee du Louvre”
}

```

## 1. geohash
- Location is divided into “grid” of cells.


```
GET my_grid_locations/_search
{
	“size”: 0,
	“aggs”: {
		“grouped”: {
			“geohash_grid”: {
				“field”: “location”,
				“precision”: 2
			}
		}
	}
}
```

- Extracing single document using bucket key:
```
GET my_grid_locations/_search
{
    “query”: {
        “geo_grid”: {
            “location”: {
                “geo_hash”: “u0”
            }
        }
    }
}
```

## 2. Geotile grid: 
- Group document depending on their geotile value
- It also divides location into a grid of cells, each represented by a unique identifier.
```
GET my_grid_locations/_search
{
    “size”: 0,
    “aggs”: {
        “my_geotile_aggs”: {
            “geotile_grid”: {
                “field”: “location”,
                “precision”: 7,
                “size”: 10
            }
        }
    }
}
```

- Extracting single document
```
GET my_grid_locations/_search
{
    “query”: {
        “geo_grid”: {
            “location”: {
                “geotile”: “7/65/42”
            }
        }
    }
}
```

## 3. geohex grid: 
- groups documents depending on their geohex values.
- Location is divided into hexagonal cells.

```
Note: (Require some licence : https://discuss.elastic.co/t/non-compliant-license-for-geohash-grid-aggregation-on-geo-shape-fields/297702/2)
```

```
GET my_grid_locations/_search
{
    “size”: 0,
    “aggs”: {
        “my_grohex_aggs”: {
            “geohex_grid”: {
                “field”: “location”,
                “precision”: 1
            }
        }
    }
}
```

- Extracting single document using geohex grid:
```
GET my_grid_locations/_search
{
	“query”: {
		“geo_grid”: {
			“location”: {
				“geohex”: “811fbffffffffff”
			}
		}
	}
}
```