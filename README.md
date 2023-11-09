Luke Ingram

INFO 250 -- Summer 2023

Final Project

# Visualization Link:  

-   <https://public.tableau.com/app/profile/luke.ingram5437/viz/IngramINFO250FinalProject/Storyboard?publish=yes>

Abstract:

Urban development and transportation infrastructure must move hand in
hand to ensure equitable and efficient access for all residents. This
visualization dives deep into the landscape of New York City\'s subway
entrance distribution across its diverse boroughs and neighborhoods,
catering to a wide range of people, from city planners to tourists.

# Research Questions: 

-   Are subway entrances equitably distributed in relation to
    neighborhood population densities?

-   Which high-population neighborhoods provide the best subway access
    for businesses and residents?

-   Are there high-density areas that lack sufficient subway services,
    or low-density areas that have an excess of subway services?

-   Is there a correlation between the population of a neighborhood and
    the count of its subway entrances/exits?

Data:

## [Demographics by Neighborhood Tabulation Area (NTA)](https://www.nyc.gov/assets/dfta/downloads/pdf/reports/Demographics_by_NTA.pdf)

The \"Demographics by Neighborhood Tabulation Area (NTA)\" dataset
provides demographic details for individual NTAs, encompassing official
names, unique codes, associated boroughs, and population figures. It
offers insights into the age distribution, with a focus on the 65+
demographic, detailing their proportion and poverty rate. The dataset
also breaks down the racial and ethnic composition by presenting
percentages for Hispanic/Latino, White, Black/African American, Asian,
and other racial/ethnic groups.

Source: NYC Department of Aging (official website)

Collection dates: November 2020

## [NYC Transit Subway Entrance And Exit Data](https://data.ny.gov/Transportation/NYC-Transit-Subway-Entrance-And-Exit-Data/i9wp-a4ja)

The dataset captures detailed attributes of NYC subway stations and
their entrances/exits. For each station, it identifies its division,
line, name, and geographic coordinates. It enumerates the routes
accessible from the station, details of the entrance type, usage
(entry/exit), availability of vending machines, staffing details, and
ADA compliance notes. Furthermore, each entrance\'s specific location,
including its latitude, longitude, nearby streets, and corner details,
are provided. Georeference identifiers for mapping are also included for
both stations and their entrances. These Georeferences will be important
for the choropleth discussed later.

Source: Ny.data.gov (Official State Government website)

Collection dates: 2022-2023

## [2010 Neighborhood Tabulation Areas (NTAs)](https://data.cityofnewyork.us/City-Government/2010-Neighborhood-Tabulation-Areas-NTAs-/cpf4-rkhq)

This dataset is only used to provide the shapefile needed to construct
the neighborhood choropleth map. By "neighborhood", we are specifically
using NYC's NTA's (Neighborhood Tabulation Area) which are geographic
units in New York City, created by the Department of City Planning, that
aggregate census tracts to approximate the city\'s recognized
neighborhoods for statistical reporting and analysis. More info can be
found
[here](https://nycplanning.github.io/Geosupport-UPG/appendices/appendix16/).

Source: NYC OpenData (Official City Government website)

Collection dates: 2010

## [NYC Subway Lines Interactive Map](https://data.cityofnewyork.us/Transportation/Subway-Lines/3qz8-muuu?category=Transportation&view_name=Subway-Lines)

This dataset provides the shapefile needed for the subway lines
component of the neighborhood choropleth map. The shapefile here
provides the geospatial data for each subway line.

Source: NYC OpenData (Official City Government website)

Collection Dates: 2017-2018

## [NYC Subway Stations Interactive Map](https://data.cityofnewyork.us/Transportation/Subway-Stations/arq3-7z49)

This dataset provides the shapefile needed to construct the subway
stations component of the neighborhood choropleth map. The shapefile
here provides the geospatial data for each subway station.

Source: NYC OpenData (Official City Government website)

Collection Dates: 2013-2019

# Methods:  

## Preparing the data:  

### Spatial Joins:  

Using [QGIS](https://qgis.org/en/site/), spatial joins were performed on
datasets 3-5. Specifics on this operation can be found
[here](https://opensource.com/article/19/2/spatial-joins-qgis).

### Data Connections  

The five datasets are delicately joined using the following logic:

![A screenshot of a computer Description automatically
generated](./media/image1.png)


Table Reference:

1.  subway_lines_revised.shp refers to a spatial join between datasets 3
    & 5.

2.  geo_export_84... refers to dataset 3.

3.  Demographics_by_NTA.csv1 refers to dataset 1.

4.  merged_lines_NYC_transit... refers to dataset 2.

5.  subway_lines_joined_shp refers to a spatial join between datasets 3
    & 4.

The connections (Relationships) between the tables are as follows:

12: Uses the "NTA Code" (Given to (1) from the spatial join) column

23: Uses the "NTA Code" column.

14: Uses the compares the Line1 & Lines_dashed columns. Lines_dashed is
added to dataset 2 with the following python script:

```python
df = pd.read_csv('NYC_Transit_Subway_Entrance_And_Exit_Data.csv')

cols = [f"Route{i}" for i in range(1,12)]

df['Lines_dashed'] = df[cols].apply(lambda row: '-'.join([str(x) for x in row if pd.notnull(x) and str(x) != '']), axis=1)
df['Lines_dashed_first'] = df[cols].apply(lambda row: '-'.join([str(x) for x in row if pd.notnull(x) and str(x) != ''])[0], axis=1)
df['Lines'] = df[cols].apply(lambda row: ', '.join([str(x) for x in row if pd.notnull(x) and str(x) != '']), axis=1)

df.to_csv("merged_lines_NYC_Transit_Subway_Entrance_And_Exit_Data.csv")
```

45: Uses the Lines_dashed_first and Rt symbol columns.
Lines_dashed_first is added to dataset 2 with the above python script.

### Calculated Fields:  

The "Stations per 10,000 people" ratio (used in visualizations 1 & 2) is
calculated in Tableau using the following formula:

-   (SUM(\[population (Demographics by NTA.csv1)\]) / 10000)

The "Number of stations per Neighborhood" (Used in all visualizations)
is calculated in Tableau with the following formula:

-   COUNT(\[Objectid (Subway Lines Revised.Shp)\])

## Visualization 1 - Interactive Map: 

In this interactive visualization, the color of each neighborhood
represents the population. Red implies higher, and green represents
lower. This provides the user with a city-wide overview of the stations
within each neighborhood, which they can navigate as they need.

![A map of the united states Description automatically
generated](./media/image3.png)


The user can scroll and zoom to focus on specific neighborhoods:![A map
of the united states Description automatically
generated](./media/image4.png)


The "Filter by Borough" option provides the user with the ability focus
on a specific borough. This is important as the boundaries between the
boroughs is not explicitly clear.

![A map of a country with red dots Description automatically
generated](./media/image5.png)


Note: it is **not** possible to crop the subway line shapes to the
selected borough. This is due to the construction of the line polygons
within the shapefile (dataset 4).

The user can also highlight specific subway stations by using the
"Highlight by Line" Feature:

![A map of a city Description automatically
generated](./media/image6.png)


Each Neighborhood, station, and line has their own tooltip, which
provides specific information about each datapoint. (Shown On click):

![A screenshot of a map Description automatically
generated](./media/image7.png)
![A map with a red and black outline
Description automatically
generated](./media/image8.png)
Neighborhood: Station:

Line:

![A map with a line drawn on it Description automatically
generated](./media/image9.png)


## Visualization 2 - Borough Analysis 

In this interactive visualization, The Treemap & bar chart show the
differences in the number of stations to 10,000 people ratio (encoded by
color). This visualization provides the user with a more comparative
look at the distribution of stations within the city.

![A screenshot of a computer screen Description automatically
generated](./media/image10.png)

The User can filter by borough, neighborhood population, and the
stations per 10,000 people ratios. This allows for direct comparisons
between different areas of the city.

![A screenshot of a computer screen Description automatically
generated](./media/image11.png)

Each leaf in the treemap, and bar on the bar chart has a tooltip, which
provides specific information on each datapoint. (Shown on Click)

![A screenshot of a number of subway stations Description automatically
generated](./media/image12.png)
![A screenshot of a computer screen
Description automatically
generated](./media/image13.png)
Leaf: Bar:

## Visualization 3 - Population vs Stations: 

This interactive scatterplot is dedicated to answering the important
question, "Does a higher population correlate to more subway stations?"

![A graph of a number of subway stations Description automatically
generated](./media/image14.png)


Even though different Boroughs are encoded using the symbols, they can
be hard to see, so the user can highlight specific Boroughs using the
"Highlight by Borough" feature.

![A graph with blue and white lines Description automatically
generated](./media/image15.png)


Each datapoint has a tooltip, which provides the user with specific
information about each datapoint. (Shown on click).

![A screenshot of a graph Description automatically
generated](./media/image16.png)


Findings:

The findings are broken down by research question:

-   Are subway entrances equitably distributed in relation to
    neighborhood population densities?

No, there does not seem to be an equitable distribution of subway
stations related to population densities. As there are several high
population neighborhoods not on the outskirts of the city, which have
little to no subway access. A few examples are, Brooklyn: Canarsie &
Flatlands, Queens: Jackson Heights & Flushing.

-   Which high-population neighborhoods provide the best subway access
    for businesses and residents?

The top neighborhood (with a population of at least 50,000) in each
borough for subway access are:

1.  Manhattan: Washington Heights North.

2.  Brooklyn: Bensonhurst West.

3.  Queens: Jamaica.

4.  Bronx: Bedford Park / Fordham North.

-   Are there high-density areas that lack sufficient subway services,
    or low-density areas that have an excess of subway services?

The first part of this question is answered by the response to the first
question. Regarding low-density areas with an excess of subway services,
a few neighborhoods stand out:

-   Midtown / Midtown-South Manhattan has 22 subway stations, with a
    population of less than 30,000. However this may be due to the
    number of commuters and tourists moving through midtown.

-   SoHo / TriBeCa Manhattan has 17 subway stations, but a population of
    about 40,000.

-   Is there a correlation between the population of a neighborhood and
    the count of its subway entrances/exits?

Visualization 3 demonstrates that there is no direct correlation between
the population of a neighborhood and the number of subway stations. This
reinforces the intuition that deciding where to put subway stations is a
process which involves a lot more information than the population of a
neighborhood.

Conclusion:

The visualization exploring Our study on New York City\'s subway
distribution has revealed some clear patterns:

-   Subway stations aren\'t evenly spread according to population
    density. High-population areas like Canarsie, Flatlands, Jackson
    Heights, and Flushing have fewer stations than one might expect.

-   On the other hand, areas like Washington Heights North in Manhattan,
    Bensonhurst West in Brooklyn, Jamaica in Queens, and Bedford
    Park/Fordham North in the Bronx have good subway access for their
    populations.

-   Some low-population areas like Midtown and SoHo/TriBeCa have many
    subway stations, likely because of daily commuters and tourists.

-   Importantly, there\'s no straightforward relationship between how
    many people live in a neighborhood and how many subway stations it
    has.

These findings show that when it comes to placing subway stations, many
factors come into play beyond just local residential numbers. As NYC
plans for the future, these insights can help in making more informed
decisions about transit and infrastructure.
