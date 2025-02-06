# InSAR Extension Specification

- **Title:** InSAR
- **Identifier:** <https://stac-extensions.github.io/insar/v1.0.0/schema.json>
- **Field Name Prefix:** insar
- **Scope:** Item
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @fabricebrito, @emmanuelmathot

This document explains the InSAR Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

Interferometric synthetic aperture radar, abbreviated InSAR (or deprecated IfSAR),
is a radar technique used in geodesy and remote sensing.
This geodetic method uses two or more synthetic aperture radar (SAR) images to generate maps of surface deformation or digital elevation,
using differences in the phase of the waves returning to the satellite or aircraft.
The technique can potentially measure millimetre-scale changes in deformation over spans of days to years.
It has applications for geophysical monitoring of natural hazards, for example earthquakes, volcanoes and landslides,
and in structural engineering, in particular monitoring of subsidence and structural stability.

The extension is primarly intented to describe **products** of InSAR processing, typically interferograms and related products.

- Examples:
  - [Simple Item example](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [EPOS Item example](examples/item-epos.json): Shows the usage of the extension in a STAC Item with [EPOS data](https://gitlab.com/epos-tcs-satdata/productmetadata/-/blob/master/samples/UNWRAPPED_INTERFEROGRAM_InU_CNRIREA_20200814_20200820_ECJT.metadata)
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Item Properties 

| Field Name                   | Type   | Description                                                                                                                                                                                                                                                                   |
| ---------------------------- | ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| insar:perpendicular_baseline | number | The distance between two acquisition spots perpendicular to the satellite viewing direction                                                                                                                                                                                   |
| insar:temporal_baseline      | number | The time period between the reference and secondary acquisitions                                                                                                                                                                                                              |
| insar:height_of_ambiguity    | number | This height of ambiguity is the 2 π interferometric phase cycle scaled with the perpendicular baseline between both satellites. The smaller the height of ambiguity is, the lower is the influence of errors caused by the instrument or the different decorrelation effects. |
| insar:reference_datetime     | string | Date of reference acquisition, in UTC. It is formatted according to [RFC 3339, section 5.6](https://tools.ietf.org/html/rfc3339#section-5.6).                                                                                                                                 |
| insar:secondary_datetime     | string | Date of secondary acquisition, in UTC. It is formatted according to [RFC 3339, section 5.6](https://tools.ietf.org/html/rfc3339#section-5.6).                                                                                                                                 |
| insar:processing_dem        | string | String representing the Digital Elevation Model used for processing the input data. A list is proposed in the [DEMs section](#digital-elevation-models-dems)                                                                                                                  |
| insar:geocoding_dem          | string | String representing the Digital Elevation Model used for geocoding the product. A list is proposed in the [DEMs section](#digital-elevation-models-dems)                                                                                                                      |

## Best Practices

### Core and other extensions fields

It is higly recommended to use the following fields to describe the InSAR product:

| Field name                                                                                                          | InSAR usage                                                                                                                             |
| ------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| [date_time](https://github.com/radiantearth/stac-spec/blob/master/item-spec/common-metadata.md#date-and-time)       | Center Time of the product, in UTC. (Here the date is not significant)                                                                  |
| [sar:instrument_mode](https://github.com/stac-extensions/sar/#item-properties-or-asset-fields)                      | REQUIRED. The name of the sensor acquisition mode that is used                                                                          |
| [sar:polarizations](https://github.com/stac-extensions/sar/#sarpolarizations)                                       | Any combination of polarizations of the input products.                                                                                 |
| [sar:looks_range](https://github.com/stac-extensions/sar/#item-properties-or-asset-fields)                          | Number of range looks, which is the number of groups of signal samples (looks) perpendicular to the flight path                         |
| [sar:looks_azimuth](https://github.com/stac-extensions/sar/#item-properties-or-asset-fields)                        | Number of azimuth looks, which is the number of groups of signal samples (looks) parallel to the flight path.                           |
| [sar:observation_direction](https://github.com/stac-extensions/sar/#item-properties-or-asset-fields)                | Antenna pointing direction relative to the flight trajectory of the satellite, either left or right.                                    |
| [sar:pixel_spacing_range](https://github.com/stac-extensions/sar/#item-properties-or-asset-fields)                 | The range pixel spacing, which is the distance between adjacent pixels perpendicular to the flight path, in meters (m).                 |
| [sar:pixel_spacing_azimuth](https://github.com/stac-extensions/sar/#item-properties-or-asset-fields)               | The azimuth pixel spacing, which is the distance between adjacent pixels parallel to the flight path, in meters (m)                     |
| [sar:resolution_range](https://github.com/stac-extensions/sar/#item-properties-or-asset-fields)                    | The range resolution, which is the maximum ability to distinguish two adjacent targets perpendicular to the flight path, in meters (m). |
| [sar:resolution_azimuth](https://github.com/stac-extensions/sar/#item-properties-or-asset-fields)                  | The azimuth resolution, which is the maximum ability to distinguish two adjacent targets parallel to the flight path, in meters (m).    |
| [processing:level](https://github.com/stac-extensions/processing#suggested-processing-levels)                       | `L3` level is recommended since InSAR is a composite product.                                                                           |
| [processing:lineage](https://github.com/stac-extensions/processing#item-properties-and-collection-provider-fields)  | Describe InSAR specific technique. For instance, `Geocoded Unwrapped Interferogram TOPSAR`                                              |
| [processing:software](https://github.com/stac-extensions/processing#item-properties-and-collection-provider-fields) | Software used for InSAR processing. For instance `{"ESA SNAP Toolbox": "8.0"}, {"SNAPHU": "1.4.2"}`                                     |
| [proj:epsg](https://github.com/stac-extensions/projection#projepsg)                                                 | EPSG code of the projection of the product                                                                                              |
| [sat:orbit_state](https://github.com/stac-extensions/sat#satorbit_state)                                            | `ascending` or `descending`                                                                                                             |
| [sat:relative_orbit](https://github.com/stac-extensions/sat#satrelative_orbit)                                      | relative orbit (track) of the input datasets                                                                                            |
| [sci:publications](https://github.com/stac-extensions/scientific#item-properties-and-collection-fields)             | DOI of publications citing the data                                                                                                     |
| [view:azimuth](https://github.com/stac-extensions/view#item-properties)                                             | The azimuth angle of the center of the product.                                                                                         |
| [view:incidence_angle](https://github.com/stac-extensions/view#item-properties)                                     | The incidence angle of the center of the product.                                                                                       |

### Assets roles

One of the emerging best practices is to use [Asset Roles](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#asset-roles) 
to provide clients with more information about the assets in an item. The following list includes a shared vocabulary for InSAR assets. 
This list should not be considered definitive, and implementors are welcome to use other asset roles. If consensus and tooling consolidates around 
these role names then they will be specified in the future as more standard than just 'best practices'.

| Role Name        | Description                                             |
| ---------------- | ------------------------------------------------------- |
| coherence        | 2D Coherence \[0-1\] from filtered interferogram          |
| phase            | 2D Filtered wrapped interferogram geocoded in radians   |
| unwrapped_phase  | 2D Filtered unwrapped interferogram geocoded in radians |
| los_displacement | 2D Displacement in the satellite line of sight (LOS)    |
| amplitude        | 2D Amplitude of interferogram in Watt                   |

Combined with "standard" asset roles `data`, `overview`, `visual` and `metadata`, specific related assets can be included.

## Digital Elevation Models (DEMs)

The DEM are named to commonly used datasets. The table below shows the common DEM dataset name based.
This list should not be considered definitive, and implementors are welcome to use other DEM names.

| DEM Name       | Description                                                                                            | Spatial Resolution   |
| -------------- | ------------------------------------------------------------------------------------------------------ | -------------------- |
| SRTM1          | Shuttle Radar Topography Mission                                                                       | 1 arcsecond (~30m)   |
| SRTM3          | Shuttle Radar Topography Mission                                                                       | 3 arcseconds (~90m)  |
| SRTM30         | Shuttle Radar Topography Mission                                                                       | 30 arcseconds (~1km) |
| COP-DEM_EEA-10 | Copernicus DEM - European states (EEA39) including all islands of those countries plus French Overseas | 10m                  |
| COP-DEM_GLO30  | Copernicus DEM - Global Coverage                                                                       | 30m                  |
| COP-DEM_GLO90  | Copernicus DEM - Global Coverage                                                                       | 90m                  |
| GTOPO30        | USGS Global Terrain Elevation Data (30m)                                                               | 30m                  |

## Relation types

The following types should be used as applicable `rel` types in the
[Link Object](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#link-object).

| Type      | Description                                               |
| --------- | --------------------------------------------------------- |
| reference | This link points to the reference input product STAC Item |
| secondary | This link points to the secondary input product STAC Item |

### Links roles

It is possible to use [Links Roles](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#link-roles)
to provide clients with more information about the linked item in an item or a collection.
The following list includes a shared vocabulary for InSAR assets.
This list should not be considered definitive, and implementors are welcome to use other asset roles. If consensus and tooling consolidates around
these role names then they will be specified in the future as more standard than just 'best practices'.

| Role Name | Description                                                               |
| --------- | ------------------------------------------------------------------------- |
| event     | Item is related to an event (e.g. earthquake, volcano eruption)           |
| coseismic | Item is related to a coseismic event or data results (e.g. interferogram) |
| post      | Item is related to a post event or data results (e.g. interferogram)      |
| pre       | Item is related to a pre event or data results (e.g. interferogram)       |

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```
