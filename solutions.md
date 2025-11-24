# GeoJson to h3 Conversion
In order to convert a GeoJson like dictionay to a list of h3 indexes, you can use the `h3` library in Python. Below is a sample code that demonstrates how to achieve this:

```python
def polygon_to_h3_cells(polygon: shapely.Polygon, resolution: str) -> list[int]:
    """Convert a polygon to a list of H3 cell indices at the specified resolution.

    Args:
        polygon: A shapely Polygon object representing the area to cover.
        resolution: The H3 resolution level.

    Returns:
        A list of H3 cell indices covering the polygon at the given resolution.
    """
    geojson = shapely.geometry.mapping(polygon)
    h3_polygon = h3.LatLngPoly(geojson["coordinates"][0])
    h3_indexes = h3.polygon_to_cells(h3_polygon, resolution)
    return h3_indexes
```
The shapely mapping converts from the polygon (lon, lat) format to the GeoJson (lat, lon) format required by the h3 library.

Note that for the conversion to work, you need to ensure that only a single polygon is provided from the coordinates of the GeoJson.

# H3 tile to partition key Conversion

To convert an H3 tile to a partition key, you can use the `h3_to_parent` function from the `h3` library. This function allows you to get the parent H3 index at a specified resolution. Below is a sample code that demonstrates how to perform this conversion:

```python
def h3_partitions_from_tiles(tiles: list[int], target_resolution: int) -> list[int]:
    """Convert a list of H3 tiles to partitions at the target resolution.

    Args:
        tiles: A list of H3 cell indices.
        target_resolution: The desired H3 resolution level for the partitions.

    Returns:
        A list of H3 cell indices at the target resolution.
    """
    partitions = []
    for tile in tiles:
        parent = h3.cell_to_parent(tile, target_resolution)
        if parent not in partitions:
            partitions.append(parent)
    return partitions
```
Not much to explain here, instead of a list a set could be also used

# H3 tile to lat/lon area

```python
def h3_tile_to_geojson_area(tile: int) -> shapely.Polygon:
    """Convert an H3 tile index to a GeoJSON Polygon representing its area.

    Note:
        H3 uses (latitude, longitude) order for coordinates, while shapely uses (longitude, latitude) order.

    Args:
        tile: An H3 cell index.

    Returns:
        A shapely Polygon representing the area of the H3 cell.
    """
    boundary = h3.cell_to_boundary(tile)
    boundary_lon_lat = [(lng, lat) for lat, lng in boundary]
    polygon = shapely.Polygon(boundary_lon_lat)
    return polygon
```

Like before, we need to swap the lat/lon order when converting from H3 to shapely.
