---
layout: post
title: 'Exploring religious data to learn about Excel & Python mapping'
published: true
---

The data sets come from the Association of Religious Data Archives (https://www.thearda.com). 
The 3 sets used are Religious Congregations and Membership Study 2000, World Religion Project - Global Religion Dataset, and the Longitudinal Religious Congregations and Membership File, 1980-2010.

A guide for making the map was used (https://plotly.com/python/choropleth-maps/). The idea was to map the various Orthodox adherents to a chloropath style map.
Additional guidance was found on Towards Data Science (https://towardsdatascience.com/choropleth-maps-101-using-plotly-5daf85e7275d).

I pre-processed the data in Excel. This was to filter down to Michigan data. What we are looking at is 3-dimensional long data that I want to analyze. 3d from the fact that it's a County,
crossed by a Group, crossed by a Year.

The problem arises when using a FIPS codes as the ID for the mapping, what happens when you have multiple data points with the same FIPS? Simply, how does the mapping function work
when you have data for multiple counties that you want to display?

