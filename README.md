# calendar

A small, dependency-free Python module for working with dates in the **proleptic Julian** and **proleptic Gregorian** calendars, and for converting them to and from **Julian Day Numbers**.

Because both calendars convert through the same Julian Day Number, the module also converts dates between the two calendars.

## Features

- `JulianCalendarDate` and `GregorianCalendarDate` classes with date validation.
- Conversion to and from Julian Day Numbers (`to_julian_day_number()`, `from_julian_day_number()`).
- Comparison operators (`<`, `<=`, `==`, `!=`, `>`, `>=`) that compare the day a date denotes, so dates in different calendars can be compared.
- `next(date)` to step to the following day.
- Leap-year and month-length helpers (`is_leapyear()`, `length_of_month()`).
- Correct handling of BCE dates. There is no year 0: year -1 (1 BCE) is followed directly by year 1 (1 CE).

## Requirements

Python 3.7 or later. No third-party packages are needed.

## Usage

Copy `calendar_dates.py` into your project, or run your code from this directory.

```python
from calendar_dates import JulianCalendarDate, GregorianCalendarDate

# Create a date and convert it to a Julian Day Number.
date = GregorianCalendarDate(2000, 1, 1)
date.to_julian_day_number()          # 2451545

# Convert between calendars via the Julian Day Number.
jdn = GregorianCalendarDate(1752, 9, 14).to_julian_day_number()
JulianCalendarDate.from_julian_day_number(jdn)
# JulianCalendarDate(year = 1752, month = 9, day = 3)

# Dates in different calendars compare by the day they denote.
JulianCalendarDate(1752, 9, 2) == GregorianCalendarDate(1752, 9, 13)   # True

# Step to the next day; 1 BCE (year -1) is followed by 1 CE.
next(JulianCalendarDate(-1, 12, 31))
# JulianCalendarDate(year = 1, month = 1, day = 1)

# Unpack a date into (year, month, day).
year, month, day = date

# Leap years and month lengths.
GregorianCalendarDate.is_leapyear(1900)       # False
JulianCalendarDate.is_leapyear(1900)          # True
GregorianCalendarDate.length_of_month(2024, 2)  # 29

# Invalid dates raise ValueError.
GregorianCalendarDate(2023, 2, 29)            # ValueError
```

### Year numbering

Years are given as signed integers: positive for CE, negative for BCE. For example, `-4713` is 4713 BCE. Year `0` does not exist and is rejected.

## How it works

- **Normalized years.** Internally, BCE years are shifted up by one (1 BCE becomes 0, 2 BCE becomes -1, and so on) so that the year sequence has no gap. This makes the arithmetic straightforward.
- **Years starting in March.** Calculations treat March as the first month of the year. February, with its possible leap day, then falls at the end of the year, so month offsets are the same in every year.
- **Leap-year cycles.** Converting a Julian Day Number back to a date peels off whole leap-year cycles: 4 years (1461 days) for the Julian calendar, and 400 / 100 / 4 years for the Gregorian calendar.

## Running the tests

```sh
python test_calendar_dates.py
```

The tests check both conversion directions for every day from Julian Day 0 up to the year 5000 in both calendars, and the comparison operators across the two calendars.
