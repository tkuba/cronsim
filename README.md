# CronSim

Cron Sim(ulator), a cron expression parser and evaluator.

Experimental and work in progress.

## Usage

```python
from datetime import datetime
from cronsim import CronSim

it = CronSim("0 0 * 2 MON#5", datetime.now())
for x in range(0, 10):
    print(next(it))
```

Outputs:

```
2044-02-29 00:00:00
2072-02-29 00:00:00
2112-02-29 00:00:00
2140-02-29 00:00:00
2168-02-29 00:00:00
2196-02-29 00:00:00
2208-02-29 00:00:00
2236-02-29 00:00:00
2264-02-29 00:00:00
2292-02-29 00:00:00
```

## Supported Cron Expression Features

CronSim aims to match [Debian's cron implementation](https://salsa.debian.org/debian/cron/-/tree/master/)
(which itself is based on Paul Vixie's cron, with modifications). If CronSim evaluates
an expression differently from Debian's cron, that's a bug.

CronSim is open to adding support for non-standard syntax features, as long as
they don't conflict or interfere with the standard syntax.

## DST Transitions

CronSim handles Daylight Saving Time transitions the same as
Debian's cron. Debian has special handling for jobs with a granularity
greater than one hour:

```
Local time changes of less than three hours, such as  those  caused  by
the  start or end of Daylight Saving Time, are handled specially.  This
only applies to jobs that run at a specific time and jobs that are  run
with  a    granularity  greater  than  one hour.  Jobs that run more fre-
quently are scheduled normally.

If time has moved forward, those jobs that would have run in the inter-
val that has been skipped will be run immediately.  Conversely, if time
has moved backward, care is taken to avoid running jobs twice.
```

See test cases in `test_cronsim.py`, `TestDstTransitions` class
for examples of this special handling.

## Cron Expression Feature Matrix

| Feature                  | Debian | croniter | cronsim |
| ------------------------ | :----: | :------: | :-----: |
| Seconds in the 6th field | No     | Yes      | No      |
| Special character "L"    | No     | Yes      | Yes     |
| N-th weekday of month    | Yes    | Yes      | Yes     |


**Seconds in the 6th field**: an optional sixth field specifying seconds. Supports
same syntax features as the minutes field.

Example: `* * * * * */15` (every 15 seconds).

**Special character "L"**: can be used in the day-of-month field and means
"the last day of the month".

Example: `0 0 L * *` (at the midnight of the last day of every month).

**N-th weekday of month**: support for "{weekday}#{nth}" syntax.
For example, "MON#1" means "first Monday of the month", "MON#2" means "second Monday
of the month".

Example: `0 0 * * MON#1` (at midnight of the first monday of every month).