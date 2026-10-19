# PowerAstron light-pollution data

This repository publishes the offline light-pollution grid that the free PowerAstron astronomy app uses on its
Home screen to show an **estimated** Bortle class and sky brightness (SQM) for the user's location.

The grid is an adapted version of a third-party dataset. It is published here, outside the app stores, so that
anyone can get it under its licence terms with no extra conditions.

| File | Size | SHA-256 |
|---|---:|---|
| [`data/falchi2016_sqm_2p5arcmin.palp`](data/falchi2016_sqm_2p5arcmin.palp) | 1,037,126 bytes | `c93692b4841e99057cc95968dde37d177b3a8b14920161cbc921f5a875ce7902` |

This is the same file that ships inside the app.

## Source

Falchi, F., Cinzano, P., Duriscoe, D., Kyba, C. C. M., Elvidge, C. D., Baugh, K., Portnov, B. A.,
Rybnikova, N. A. & Furgoni, R. (2016). *The New World Atlas of Artificial Night Sky Brightness.*
GFZ Data Services, [doi:10.5880/GFZ.1.4.2016.001](https://doi.org/10.5880/GFZ.1.4.2016.001).

Falchi, F. et al. (2016). The new world atlas of artificial night sky brightness. *Science Advances* 2(6):
e1600377, [doi:10.1126/sciadv.1600377](https://doi.org/10.1126/sciadv.1600377).

## Licence

The source atlas is licensed under
[Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/).
The GFZ Data Services record notes "13 November 2019: change of the licence to CC BY NC 4.0 (after end of embargo
period)".

This adapted grid is also licensed under **CC BY-NC 4.0**. The full licence text is in [LICENSE](LICENSE). You
may share and adapt it for non-commercial purposes if you give appropriate credit to the original authors (the
citation above), link to the licence and indicate changes.

**No endorsement:** the original authors and GFZ have not reviewed, approved or endorsed this adapted grid or
the PowerAstron app.

## Changes from the original

- **Input:** the 2015 artificial sky brightness model at 30 arcseconds.
- **Resampled** to a 2.5 arcminute grid (about 4.6 km at the equator) by taking the median of each 5 × 5 block.
- **Converted** from artificial sky luminance to total sky brightness in mag/arcsec² (SQM), adding a fixed
  natural-sky term: `SQM = -2.5 × log10((artificial + 0.171168465) / 1.08e8)`, with luminance in the atlas's
  units (mcd/m²).
- **Quantized** to 0.1 mag steps, with values adjusted so that each cell's Bortle class (bounds below) matches
  the class of the unrounded value ("Bortle-class locking").
- **Encoded** in a compact tiled binary format; see [FORMAT.md](FORMAT.md).

## What the values mean, and their limits

- It is a **model estimate, not a measurement or a forecast.** It represents artificial light as modelled for
  2015, plus a fixed natural baseline.
- It does not include the Moon, weather, airglow, aurora, lighting changes since 2015, local shielding or
  variation within a cell.
- **Coverage:** about 85°N to 60°S. Cells outside the grid, or marked no-data, have no value.
- **Bortle class from SQM:** an approximate community convention, not a standardized conversion or an
  observational classification. Lower SQM bound per class:
  - 1: ≥ 21.99
  - 2: ≥ 21.89
  - 3: ≥ 21.69
  - 4: ≥ 20.49
  - 5: ≥ 19.50
  - 6: ≥ 18.94
  - 7: ≥ 18.38
  - 8: ≥ 17.80
  - 9: below 17.80
- **Agreement with the full-resolution atlas:**
  - Bortle class in transition zones: 92.8% exact, 99.99% within one class.
  - Lit areas: 93.9% exact.
  - SQM absolute error: median 0.06 mag, 95th percentile 0.33 mag.

  These figures measure the resampling error only, not accuracy in the field.

## Integrity

```sh
sha256sum -c SHA256SUMS
```

On Windows PowerShell: `Get-FileHash data\falchi2016_sqm_2p5arcmin.palp -Algorithm SHA256`.
