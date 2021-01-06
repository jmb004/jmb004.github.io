---
layout: post
title: 'Exploring religious data to learn about Excel & Python mapping'
published: true
---

The data sets come from the Association of Religious Data Archives (https://www.thearda.com). 
The 3 sets used are Religious Congregations and Membership Study 2000, World Religion Project - Global Religion Dataset, and the Longitudinal Religious Congregations and Membership File, 1980-2010.

A guide for making the map was used (https://plotly.com/python/choropleth-maps/). The idea was to map the various Orthodox adherents to a chloropath style map.
Additional guidance was found on Towards Data Science (https://towardsdatascience.com/choropleth-maps-101-using-plotly-5daf85e7275d).

I pre-processed the data in Excel. This was to filter down to Michigan data. What we are looking at is 3-dimensional long data that I want to analyze. 3d from the fact that it's a County, crossed by a Group, crossed by a Year.

The problem arises when using a FIPS codes as the ID for the mapping, what happens when you have multiple data points with the same FIPS? Simply, how does the mapping function work when you have data for the same counties that you want to display?

There a few options: 1) reduce the variables in the data set to only 2 (i.e., Year and Group) so that the map is a graph. Which it sort of is. Or, nest the Adherent (number variable) inside the labels and use the scale to show the various groups. This would only look different and maybe be harder to interpret quickly. The question is, what do I want to show?

First, I decided to simplify the dataset to 1 year, 2010, and change the scope of the interesting variable (Orthodox denominations) into 1 variable of interest. This allows for a strict 2-variable scale to show the number of adherents by county. You get a snap-shot as opposed to scalar dataset.

INSERT MAP

I used the second options found in the function to create a "subplot" to show that data. Using the facet_row method, it creates multiple maps for each variable selected. It looks messy (https://drive.google.com/file/d/1enOA_yulf64w1Oz_oL6UTmm3ShZUtM8Z/view?usp=sharing) even with only a little over a dozen grouping variables.

The second variation was to use the animation_frame method to showcase the various groups while still only in 1 year. While "unorthodox" in method (pun intended) since this parameter is usually for time, it is a way to see the geographical division by group. 

INSERT

This discovery, that a chloropleth map is actually a plot with geo on the vertical axis is one that helps shape the way the data is pre-processed to enter correctly into the mapping function. While other solutions may exist, it seems that this is the easiet route. I have not read of other users finding this to be a problem with there data. In fact, the assumption seems to be that the map processes the merging of FIPS rows in a satisfactorly way. If that's correct, I have to disagree.

Code below
------
import json,os
import plotly.express as px
import pandas as pd
from urllib.request import urlopen

# Set directory
d = os.getcwd()

# Pull data to be mapped
df = pd.read_csv(d+r"\ReligiousCongregationsandMembershipStudy2000_MI.csv",dtype={"FIPSMERG":str})
#df = pd.read_csv(d+r"\EOrtho_MI.csv",dtype={"FIPSMERG":str})

print(df.columns)
print("Dropping empty rows...")
df.isna()
df.dropna(axis=0, how="any")

# Create map
print("Getting map...")
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)
    
counties["features"][0]
#print(counties["features"][0].keys())

# Create choropleth map with data
print("Making map...")
fig = px.choropleth(df,
                    geojson=counties, # pull counties from the geojson
                    locations="FIPSMERG", # where to get geo-data
                    color="ADHERENT", # what data to scale
                    color_continuous_scale="Viridis", # color pattern of scale?
                    range_color=(df['ADHERENT'].min(), df['ADHERENT'].max()), # set the color number range
                    scope="usa", # starting map view scale
                    hover_name = "CNTYNM",
                    hover_data = ["TOTPOP"], # labels on hover
                    title = "Eastern Orthodox Christians in Michigan in 2010",
                    labels = {"FIPSMERG":"FIPS","GRPNAME":"Jurisdiction","ADHERENT":"Adherents","TOTPOP":"Population"},
                    #facet_row = "GRPNAME"
                    animation_frame = "GRPNAME",
                    )

#Index(['FIPSMERG', 'CNTYNM', 'TOTPOP', 'GRPNAME', 'ADHERENT'], dtype='object'

# Set map view
fig.update_geos(fitbounds="locations")
"""
fig.update_layout(margin={"r":0,"t":0,"l":0,"b":0},
                  title_text = ,
                  title_font_size=30
                  )
"""

# Display choropleth
fig.show()
