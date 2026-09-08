# Data Dictionary

This concise dictionary documents the final semantic model, the grain of each table, and the fields and concepts most important for interpreting the report.

## Fact tables

| Table | Grain | Rows | Primary analytical use |
| --- | --- | ---: | --- |
| `Off Plan` | One modelled off-plan listing | 12,000 | Project, developer, price, PPSF, launch/handover and payment-plan analysis |
| `Rentals` | One modelled rental listing | 25,000 | Annual rent, rental PPSF, furnishing, cheque terms and rental activity |
| `Secondary Sales` | One modelled secondary-market listing | 50,000 | Resale price, PPSF, property characteristics and location analysis |
| `Area Prices Monthly` | One community-month observation | 6,384 | Monthly market-price trends and community/zone coverage |

## Dimension tables

| Table | Purpose | Connected fact tables |
| --- | --- | --- |
| `Dim Date` | Shared calendar and time-intelligence filtering | All four fact tables |
| `Dim Community` | Shared community and zone filtering; contains all 84 communities | All four fact tables |
| `Dim Project` | Shared off-plan project attributes | Off Plan |
| `Dim Metro Station` | Station, line and accessibility attributes | Off Plan, Rentals and Secondary Sales |
| `Dim Bedrooms` | Bedroom values 0–6 and readable bedroom labels | Rentals and Secondary Sales |

## Confirmed relationship fields

| From | To | Relationship |
| --- | --- | --- |
| `Dim Bedrooms[Bedrooms]` | `Rentals[Bedrooms]` | One-to-many, active, single-direction |
| `Dim Bedrooms[Bedrooms]` | `Secondary Sales[Bedrooms]` | One-to-many, active, single-direction |

The remaining conformed relationships follow the same one-to-many, active, single-direction design using shared date, community, project and metro-station keys.

## Important source fields

| Business field | Source area | Meaning |
| --- | --- | --- |
| Date listed | Listing facts | Date on which the modelled listing entered the dataset |
| Community / Zone | Listing facts and Area Prices Monthly | Geographic market segmentation |
| Property category / type | Listing facts | Broad and detailed property classification |
| Bedrooms | Off Plan, Rentals and Secondary Sales | Bedroom count; zero is presented as Studio in the shared dimension |
| Area (sq ft / m²) | Listing facts | Property size |
| Price (USD) | Off Plan and Secondary Sales | Modelled asking/listing price |
| Price per sq ft (USD) | Off Plan and Secondary Sales | Price standardised by property area |
| Annual rent (USD) | Rentals | Modelled annual asking rent |
| Furnishing status | Rentals | Furnished, semi-furnished or unfurnished status |
| Number of cheques | Rentals | Contract payment-frequency indicator |
| Metro station / line | Listing facts and metro dimension | Nearest-station context |
| Metro travel time | Listing facts | Estimated accessibility measure |
| Distance to Burj Khalifa | Listing facts / metro data | Location-proximity measure in kilometres |
| Launch / handover year | Off Plan | Project development timeline |
| Payment plan | Off Plan | Developer payment-plan category |
| Mortgage rate at listing | Off Plan | Financing-rate context at the listing date |

## PBIX-verified report fields

The final report visuals directly reference the following descriptive fields:

| Table | Fields used in visuals |
| --- | --- |
| `Dim Date` | `Month Start`, `Year` |
| `Dim Community` | `Community` |
| `Dim Bedrooms` | `Bedroom Label` |
| `Off Plan` | `Property type`, `Metro Travel Time Category` |
| `Rentals` | `Furnishing`, `Property Type` |
| `Secondary Sales` | `Bedroom Label`, `Condition`, `Furnishing`, `Property Type` |

This is a visual-use inventory rather than an exhaustive export of every technical column in the semantic model.

## Business definitions

- **Off plan:** Property marketed or sold before construction is complete and before final handover.
- **Secondary market:** Property resold by an existing owner or investor rather than the developer's initial sale.
- **Price per sq ft (PPSF):** Property price divided by its area in square feet.
- **Off-plan PPSF premium:** Relative difference between average off-plan PPSF and average secondary-market PPSF.
- **Estimated gross rental yield:** Average annual rent divided by average secondary-market property price under the same filter context.
- **Rental listing record:** One row in the Rentals table; it is not necessarily a confirmed unique physical property.
- **Community:** Named Dubai market area used for geographic comparison.
- **Bedroom label:** User-friendly grouping where zero bedrooms is shown as Studio and other values are shown as 1BR–6BR.

## Interpretation cautions

- Yield is an aggregate market-screening metric because rental and sales records are not matched by property/unit ID.
- Different fact-table grains can produce different weighting and therefore different aggregate values.
- 2026 is partial through April and must be treated as year-to-date where relevant.
- Floor-related Secondary Sales fields have approximately 36% missing values and should not be interpreted as complete coverage.
