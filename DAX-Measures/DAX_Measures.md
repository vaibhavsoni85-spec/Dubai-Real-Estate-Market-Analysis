# Key DAX Measures

This document presents 12 selected measures from the final Power BI report. The selection focuses on core KPIs, reusable base measures, cross-table business calculations and time intelligence rather than documenting every simple or supporting measure in the semantic model.

The formulas were copied from the final PBIX and reviewed for syntax, naming and measure dependencies.

## Measure dependency summary

| Derived measure | Supporting measures |
| --- | --- |
| `Off Plan Price per Sq Ft Premium %` | `Average Off Plan Price per Sq Ft`, `Average Secondary Price per Sq Ft` |
| `Estimated Gross Rental Yield %` | `Average Annual Rent`, `Average Secondary Price` |
| `Average Annual Rent YoY %` | `Average Annual Rent`, `Average Annual Rent PY` |

## 1. Average Off Plan Price

**Purpose:** Calculates the average modelled off-plan property price within the current report filter context.

```DAX
Average Off Plan Price =
AVERAGE('Off Plan'[Price (usd)])
```

**Business use:** Establishes the off-plan market price benchmark used on the Market Overview and Off Plan vs Secondary pages.

## 2. Average Secondary Price

**Purpose:** Calculates the average modelled resale property price within the current filter context.

```DAX
Average Secondary Price =
AVERAGE('Secondary Sales'[Price (usd)])
```

**Business use:** Provides the secondary-market benchmark used in price comparisons, estimated yield and location analysis.

## 3. Average Annual Rent

**Purpose:** Calculates average annual asking rent from the Rentals fact table.

```DAX
Average Annual Rent =
AVERAGE('Rentals'[Annual Rent (USD)])
```

**Business use:** Supports rental-market benchmarking, year-over-year analysis and the estimated gross rental-yield calculation.

## 4. Average Off Plan Price per Sq Ft

**Purpose:** Calculates the average off-plan price per square foot.

```DAX
Average Off Plan Price per Sq Ft =
AVERAGE('Off Plan'[Price per Sq ft (usd)])
```

**Business use:** Standardises off-plan prices for comparisons between properties and communities of different sizes.

## 5. Average Secondary Price per Sq Ft

**Purpose:** Calculates the average secondary-market price per square foot.

```DAX
Average Secondary Price per Sq Ft =
AVERAGE('Secondary Sales'[Price per Sq ft (usd)])
```

**Business use:** Provides the comparable resale PPSF benchmark required for evaluating the off-plan premium.

## 6. Off Plan Price per Sq Ft Premium %

**Purpose:** Measures the percentage by which average off-plan PPSF is above or below average secondary-market PPSF.

```DAX
Off Plan Price per Sq Ft Premium % =
VAR OffPlanPPSF =
    [Average Off Plan Price per Sq Ft]
VAR SecondaryPPSF =
    [Average Secondary Price per Sq Ft]
RETURN
    IF(
        ISBLANK(OffPlanPPSF) ||
        ISBLANK(SecondaryPPSF),
        BLANK(),
        DIVIDE(
            OffPlanPPSF - SecondaryPPSF,
            SecondaryPPSF
        )
    )
```

**Business use:** A positive result indicates that off-plan property is priced at a premium per square foot; a negative result indicates a discount. The blank checks prevent misleading results when either market lacks data in the selected context.

## 7. Estimated Gross Rental Yield %

**Purpose:** Divides average annual rent by average secondary-market price within the same filter context.

```DAX
Estimated Gross Rental Yield % =
DIVIDE(
    [Average Annual Rent],
    [Average Secondary Price]
)
```

**Business use:** Provides an aggregate market-screening indicator for comparing communities and bedroom categories. It is not a matched-property investment return because rental and sale listings are not linked at individual unit level.

## 8. Rental Listing Records

**Purpose:** Counts rows from the Rentals fact table after applying the active filters.

```DAX
Rental Listing Records =
COUNTROWS('Rentals')
```

**Business use:** Measures rental-listing activity. It should not be interpreted as a confirmed count of unique physical properties.

## 9. Average Annual Rent PY

**Purpose:** Returns average annual rent for the equivalent date context one year earlier.

```DAX
Average Annual Rent PY =
CALCULATE(
    [Average Annual Rent],
    DATEADD('Dim Date'[Date], -1, YEAR)
)
```

**Business use:** Creates the prior-year comparison value needed for year-over-year rental analysis and respects the active `Dim Date` filter context.

## 10. Average Annual Rent YoY %

**Purpose:** Calculates percentage growth or decline in average annual rent relative to the equivalent prior-year period.

```DAX
Average Annual Rent YoY % =
DIVIDE(
    [Average Annual Rent] -
    [Average Annual Rent PY],
    [Average Annual Rent PY]
)
```

**Business use:** A positive result indicates rental growth, while a negative result indicates a decline. Because the calculation uses the active date context, partial-year periods can be compared with the equivalent prior-year period.

## 11. Monthly Off Plan Listings

**Purpose:** Sums modelled off-plan listing activity from the monthly area-price table.

```DAX
Monthly Off Plan Listings =
SUM('Area Prices Monthly'[Off Plan Listings])
```

**Business use:** Supports monthly trend analysis and market-segment activity comparisons on the Market Overview page.

## 12. Monthly Secondary Listings

**Purpose:** Sums modelled secondary-market listing activity from the monthly area-price table.

```DAX
Monthly Secondary Listings =
SUM('Area Prices Monthly'[Secondary Listings])
```

**Business use:** Provides the secondary-market activity series used alongside off-plan and rental listing trends.

## Additional PBIX measure inventory

The final report also references the following measures. Their formulas remain available in the PBIX but are not repeated here because the 12 measures above provide the clearest portfolio evidence of the core analytical logic.

- `Annual Rent YoY Display`
- `Average Area Sq Ft`
- `Average Bedrooms`
- `Average CBUAE Base Rate`
- `Average Distance to Burj Khalifa`
- `Average Metro Travel Time`
- `Average Monthly Off Plan Price per Sq Ft`
- `Average Monthly Rent per Sq Ft`
- `Average Monthly Secondary Price per Sq Ft`
- `Average Mortgage Rate`
- `Average Rent per Sq Ft`
- `Monthly Rental Listings`
- `Total Off Plan Listings`
- `Total Rental Listings`
- `Total Secondary Listings`

`Metro Travel Time Category` is used with an implicit minimum aggregation in one report visual; it is not an explicit DAX measure.

## Validation notes

- Currency measures should use the report's USD formatting.
- Percentage measures should be formatted as percentages rather than multiplied by 100 in DAX.
- Count measures should use whole-number formatting.
- Results remain filter-context dependent and should respond to the dimensions connected to their respective fact tables.
