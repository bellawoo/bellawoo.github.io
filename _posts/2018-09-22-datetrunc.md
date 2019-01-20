---
layout: post
title:  Time-Series Aggregation with Django Queryset
date:   2018-09-22
categories: blog
---

It's becoming more frequent for me to need to build data visualizations that express trends over a period of time. Data is aggregated by some quantitative expression (such as a count or an average), grouped by a common time particle (such as by year). This is known as a time-series.

## What's in a timestamp?
No matter what language you are using, timestamps contain much of the same elements. Here are the parts of a datetime object that is valid for both Python and SQL when using ISO 8601 formatting. 

| YY   | MM    | DD  | T                      | hh   | ss     | mmmmmm       |
|------|-------|-----|------------------------|------|--------|--------------|
| year | month | day | _marks switch to time_ | hour | second | milliseconds |

## OK, so there's datetime.datetime for that
Most of the time, we don't care about the seconds or milliseconds. We could just extract those extraneous elements using the datetime module in Python.

    my_date = datetime(2018, 9, 22, 14, 25, 40, 631958)

    my_date.day
    >>> 22

    my_date.date()
    >>> datetime.date(2018, 9, 22)

But that requires a lot of manipulation if we want to work between different time elements. We'd be creating a new datetime object for every single record. For example, if we're tracking airfare prices, we probably don't need to know about the changes happening every minute. We would like to see if there are trend differences over days of the week or if there's been a stead rise in the average price over the past couple of years.

It seems like it would be really handy to be able to take a dataset and organize it into groups based on common units of time.

## SQL's awesome DATE_TRUNC()
If you're familiar with SQL, you're probably thinking you need to `GROUP_BY` the time. But how is it possible to group like timestamps together if each timestamp is unique.... or as unique as it gets when it stores milliseconds within the timestamp?

Enter `DATE_TRUNC()`, a SQL datetime function. It takes datetimes and rounds them to a specified precision. The following are the units of time that the function accepts as an argument:
- microseconds
- milliseconds
- second
- minute
- hour
- day
- week
- month
- quarter
- year
- decade
- century
- millenium

Examples:

    SELECT DATE_TRUNC('month', '2018-09-22 14:25:40');
    >>> 2018-09-01 00:00:00

    SELECT DATE_TRUNC('hour', '2018-09-22 14:25:40');
    >>> 2018-09-22 14:00:00

We can use this function to `GROUP_BY` datetime and aggregate over other interesting fields.

    SELECT DATE_TRUNC('year', flight_date) AS year,
      AVG(cost) as cost
    FROM fake_flight_fares
    WHERE flight_date >= '2018-01-01'
      AND flight_date <= '2018-12-31'
    GROUP BY 1
    ORDER BY 1

If we wanted to look at the average cost of a flight by month, all we'd have to do is replace `year` with `month`.

## Django's abstracted this already with Trunc()
Often, I'm not running raw SQL queries for data analysis. I'm building a feature to expose this information to users. If we were building our app using Django, how could we accomplish this same aggregation by datetime?

    from datetime import datetime
    from django.db.models import COUNT
    from django.db.models.functions import Trunc

    Flights.objects.create(flight_datetime=datetime(2018, 9, 22, 14, 25, 40, 631958))
    Flights.objects.create(flight_datetime=datetime(2018, 1, 2, 22, 5, 15, 723526))
    Flights.objects.create(flight_datetime=datetime(2017, 6, 30, 4, 13, 35, 247385))

    flights_by_year = Flights.objects\
      .annotate(flight_year=Trunc('flight_datetime', 'year'))\
      .values('flight_year')\
      .annotate(flights=Count('id'))

    >>> 2018-01-01 00:00:00   2
        2017-01-01 00:00:00   1

Magic!

With Django's built-in database function for date truncation, it is easy to create aggregated time-series data. There's no manipulating datetime objects and having to calculate timedeltas to work with new time units. We just need to run the same function but with a different time unit.

As I'm diving more into time-series visualizations, the discovery of `DATE_TRUNC` and `Trunc()` is one of my best to date. I've worked on time-series visualizations where a similar function didn't exist with the technology I was using and I spent a lot of effort and lines of code grouping information togather and munging datetime objects. It's super useful and I think I'll find myself using it a lot more in the future.
