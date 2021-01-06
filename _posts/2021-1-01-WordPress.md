---
layout: post
title: 'Exploring religious data to learn mapping with plotly.express'
published: true
---

My interest was to do some basic visual data analysis of a religion in the state of Michigan.

The data set, Religious Congregations and Membership Study 2000, came from the [Association of Religious Data Archives](https://www.thearda.com). 

**Making the map**
A guide for making the map was [used](https://plotly.com/python/choropleth-maps/). The idea was to map the various Orthodox adherents to a chloropath style map.
Additional guidance was found on [Towards Data Science](https://towardsdatascience.com/choropleth-maps-101-using-plotly-5daf85e7275d).

I pre-processed the data in Excel. This was to filter down to Michigan data. What we are looking at is 3-dimensional long data that I want to analyze. 3d from the fact that it's a County, crossed by a Group, crossed by a Year.

**The Problem**
The problem arises when multiple data points have the same FIPS. Simply, how does the mapping function work to read in the data accounting for this?

**Possible Solutions**
There a few options: reduce the variables in the data set to only 2 (i.e., reduce the variables Year and Group to 1) so that the map is a plot of 2 variables. Or, use another parameter of the function to create a 3rd dimension. This would only look different and maybe be harder to interpret quickly. The question is, what do I want to show?

First, I decided to simplify the dataset to 1 year, 2010, and change the scope of the interesting variable (Orthodox denominations) into 1 variable of interest. This allows for a strict 2-variable scale to show the number of adherents by county. You get a snap-shot as opposed to scalar dataset.

Format: ![pre-processed](/images/EO_2010.png)

I used the second option found in the [function](https://plotly.com/python-api-reference/generated/plotly.express.choropleth) to create a "subplot" to show that data. Using the facet_row method, it creates multiple maps for each variable selected. It looks messy even with only a little over a dozen grouping variables.

![framed_col](/images/facet_row.png)

Then I remembered another method: use the animation_frame method to showcase the various groups while still only in 1 year. While "unorthodox" (pun intended) in method, since this parameter is usually for time, it is a way to see the geographical division by group. 

Format: ![animation_frame](/images/animation.png)

This discovery, that a chloropleth map is actually a plot with geophical data (FIPS here) on the vertical axis is one that helps shape the way the data is fed into the mapping function. While other solutions may exist, it seems that that pre-processing the data to remove or reduce a variable is the easiet route. I have not read of other users finding this to be a problem with there data. In fact, the assumption seems to be that the map processes the merging of FIPS rows with multiple datapoints in a satisfactorly way. If that's a correct assumption I have to disagree.

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
