# Consecutive Well Shutdown Analysis

## Overview

How long was a well actually shut in?

Monthly downtime totals do not always provide a complete picture of well performance. For example, 10 days of downtime in a month could represent one continuous 10-day shutdown or ten separate one-day interruptions. These situations may have very different operational causes and require different responses.

This project demonstrates how to identify and extract consecutive well shutdown periods from daily production data using **Python and Pandas**.

The main objective is to transform daily well status records into a structured list of shutdown events, including their start date, end date, and duration.

## Objectives

- Identify consecutive zero-value periods for each well.
- Detect the beginning and end of individual shutdown events.
- Calculate the duration of each shutdown period.
- Handle gaps in daily records to avoid incorrectly grouping separate shutdown periods.
- Generate a structured output for further production and operational analysis.

## Input Data

The analysis requires a dataset containing the following columns:

| Column | Description |
|---|---|
| `well_id` | Unique identifier for each well |
| `date` | Daily observation date |
| `timedown` | Daily downtime or well status indicator; zero represents a shut-in condition |

Example:

| well_id | date | timedown |
|---|---|---:|
| 101 | 2024-01-01 | 0 |
| 101 | 2024-01-02 | 0 |
| 101 | 2024-01-03 | 0 |
| 101 | 2024-01-04 | 5 |
| 101 | 2024-01-05 | 0 |
| 101 | 2024-01-06 | 0 |

## Methodology

The analysis follows four main steps:

**1. Data preparation**

Load the dataset, convert the date column to datetime format, and sort records by well ID and date.

**2. Identify shutdown status**

Create a Boolean indicator to identify records where `timedown` equals zero.

**3. Group consecutive periods**

Use `groupby()`, `shift()`, and `cumsum()` to generate a unique group identifier whenever:

- The well status changes from shut-in to operating or vice versa.
- The gap between consecutive observation dates exceeds one day.

This approach ensures that missing dates do not incorrectly extend a continuous shutdown period.

**4. Extract shutdown events**

Filter zero-value records and aggregate each shutdown group to calculate:

- Shutdown start date
- Shutdown end date
- Number of consecutive shutdown records

## Key Python Concepts

The analysis uses several Pandas functions:

- `groupby()` – Performs well-level grouping.
- `shift()` – Compares each record with the previous observation.
- `cumsum()` – Creates sequential group identifiers.
- `agg()` – Extracts the start date, end date, and duration of each shutdown event.

## Output

The final output is a table containing individual shutdown events for each well.

| well_id | start | end | consecutive_days |
|---|---|---|---:|
| 101 | 2024-01-01 | 2024-01-03 | 3 |
| 101 | 2024-01-05 | 2024-01-06 | 2 |

Each row represents one continuous shutdown period.

## Potential Applications in Oil & Gas

Identifying consecutive shutdown periods can support several production and operational analyses:

- **Production Surveillance:** Monitoring well availability and operating patterns.
- **Workover Prioritization:** Identifying wells with frequent or extended shutdown periods.
- **Production Deferment Analysis:** Evaluating production losses associated with well shutdowns.
- **Reliability Analysis:** Supporting Mean Time Between Failures (MTBF) and Mean Time To Repair (MTTR) calculations when appropriate event data is available.
- **Operational Performance:** Distinguishing isolated interruptions from extended shutdown events.

## Data Considerations

A few important considerations should be addressed before applying the method to real production data:

- **Missing Dates:** A gap of more than one day starts a new group. Missing dates are not automatically filled.
- **Duplicate Records:** Each well should ideally have only one record per date. Duplicate records should be investigated and resolved before calculating shutdown durations.
- **Zero vs. NaN:** A zero value is treated as a shut-in condition, while NaN is not considered zero. Missing values should be investigated rather than automatically interpreted as operational downtime.
- **Operational Context:** Not every zero represents an equipment failure. Planned shutdowns, operational restrictions, and field-wide interruptions should be considered when interpreting the results.

The calculated duration represents consecutive daily records, assuming one record per well per day.

## Tools & Libraries

- Python
- Pandas
- Jupyter Notebook

## Repository Contents

```text
downtime_cumsum/
│
├── cumsum_downtime.ipynb
└── README.md
```

## Conclusion

A simple sequence of zero values can reveal much more than a monthly downtime total when analyzed in the right context.

By identifying individual shutdown events, this approach provides a more detailed view of well operating history and creates a foundation for further production engineering and reliability analysis.

**The goal is not just to count downtime, but to understand its patterns and operational significance.**
