## Visualizing use of New York City's CitiBike: June 2013
Using an export of trip data provided by [CitiBike](https://citibikenyc.com/system-data), I created the following Power BI dashboard. Data is sourced from June 2013, which is the first full month of operation in NYC. Paired with the trip data, is weather data sourced from [NOAA](https://www.ncei.noaa.gov/cdo-web/datasets/GHCND/locations/CITY:US360019/detail).

 The next logical extension for this dashboard is to include the latest data to see how ridership habits have changed. Here is the main page of the dashboard. 
![dashboard](/assets/images/Citi_bike_dashboard.png "CitiBike Power BI Dashboard")

# Key Features of the dashboard
1. To separate the data visualizations from the intractable  filters, the option to filter by Gender, Trip Start Date, Age, and User Type are collapsed into a pane on the left side of the canvas.

![dashboard](/assets/images/Citi_bike_dashboard_expanded_filters.png "CitiBike Power BI Dashboard - Filters")

2. The dashboard also demonstrates the use of custom tool tips. This allows layering one visual on top of another, and can be shown to a specific data point. For example. I show breakdown of User Type (where 'Subscriber' represents a rider who pays a recurring monthly fee for the use of CitiBikes and a 'Customer' who pays on a per ride basis) by start location. This pie chart only appears when hovering over a location on the map visual:

![dashboard](/assets/images/Citi_bike_dashboard_visible_tooltip.png "CitiBike Power BI Dashboard - Filters")

Insights:
1. As we would expect, the daily maximum temperature (measured in degrees Farenheight) is negatively correlated with the average trip time.
2. Looking at the distinct count of trip starts, the most popular locations for beginning a ride are located in Midtown Manhattan
3. By filtering the map visual on start and end locations we see early adopters of CitiBike made Brooklyn the most popular destination based during the month of June
![dashboard](/assets/images/Citi_bike_dashboard_expanded_filters.png "CitiBike Power BI Dashboard - Filters")
