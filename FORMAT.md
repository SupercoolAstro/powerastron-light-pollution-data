# PALP grid format, version 1

All values are little-endian.

## Header (64 bytes)

| Offset | Type | Field | Value in the published file |
|---:|---|---|---|
| 0 | 4 bytes | magic | `PALP` |
| 4 | u16 | version | 1 |
| 6 | u8 | quant: 1 = SQM code, 2 = Bortle class | 1 |
| 7 | u8 | filter: 0 none, 1 per-row delta, 2 per-column delta (mod 256) | 0 |
| 8 | f64 | west edge longitude, degrees | -180.0 |
| 16 | f64 | north edge latitude, degrees | 85.0541668645 |
| 24 | f64 | cell size, degrees | 0.04166665 (2.5′) |
| 32 | u32 | width (columns) | 8640 |
| 36 | u32 | height (rows) | 3482 |
| 40 | u16 | tile size, cells | 256 |
| 42 | f32 | q0 | 16.0 |
| 46 | f32 | qStep | 0.1 |
| 50 | u8 | no-data code | 255 |
| 51 | | zero padding to byte 64 | |

## Tile table

Right after the header there are `ceil(height / tile) × ceil(width / tile)` entries in row-major order. Each
entry is a `u32 offset, u32 length` pair:

- `length == 0`: the whole tile is the constant code `offset & 0xFF`.
- Otherwise, bytes `[offset, offset + length)` of the file are a zlib (RFC 1950) stream.
  - It inflates to the tile's codes in row-major order, `tileHeight × tileWidth` bytes. Tiles on the
    right and bottom edges are smaller.
  - If `filter` is not 0, undo the delta filter, mod 256: per row for filter 1, per column for filter 2.

## Lookup

1. Normalize the longitude to [-180, 180): `lon = ((lon + 180) mod 360) - 180`.
2. Compute the cell:
   - `row = floor((north - lat) / cell)`
   - `col = floor((lon - west) / cell)`

   Use floor, never truncation toward zero; otherwise points just north of the grid would wrongly land in
   row 0.
3. If `col == width` (floating-point rounding at the antimeridian), use `width - 1`.
4. A row outside `[0, height)`, a column below 0, or code 255 means **no data**.
5. For quant 1, `SQM = q0 + code × qStep` (mag/arcsec²). For quant 2, the code is the Bortle class.

Reference checks for the published file:
- 85.0750001895°N, 0° is outside the grid.
- Longitudes 180° and 179.99995° at latitude 0° are inside.
- -60.03° latitude is outside.
