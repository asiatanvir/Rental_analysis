# Housing Rental Analysis for San Francisco¶

This jupyter note book dealt with data visualization skills, including aggregation, interactive visualizations, and geospatial analysis, to find properties in the San Francisco market that are viable investment opportunities.

The main task in this Challenge was to visualize and analyze the real-estate data in Jupyter notebook by using hvPlot and GeoViews. It includeed: 

* Calculate and plot the housing units per year.

* Calculate and plot the average prices per square foot.

* Compare the average prices by neighborhood.

* Build an interactive neighborhood map.

### Calculate and Plot the Housing Units per Year¶

In this part numerical and visual aggregation was used to calculate the number of housing units per year, and then a bar chart was created to show results.


```
        # Slic housing units and year from San Francisco created data frame.
          housing_units_by_year =sfo_data_df[["housing_units"]]
          
        # Create a numerical aggregation that groups the data by the year and then averages the results.
         housingunits_gp= housing_units_by_year.groupby("year").mean()
        

 ```

![San Francisco housing](./Images/housing-units-by-year.png)

##### Generated bar chart depicted that over all trend in housing units was rising over the years from 2010-2016.
   
### Calculate and Plot the Average Sale Prices per Square Foot

To plot the Average Sale Prices per Square Foot and Gross Rent,original sfo_sales_df was grouped by year and then averaged the results. From the generated dataframe, the housing units column was filtered out and the new DataFrame included the averages per year for only the sale price per square foot and the gross rent. Through hv line plot, both prices_square_foot_by_year and gross_rent_per_year were visualized in a single plot.

```
       #Step1
       sfo_sales_df =sfo_data_df.drop(columns="neighborhood")
       sfo_sales_avg_data=sfo_sales_df.groupby("year").mean()
      
      #Step2
      prices_square_foot_by_year = sfo_sales_avg_data[["sale_price_sqr_foot","gross_rent"]]
    
      
```

![avg sale price](./Images/avg-sale-px-sq-foot-gross-rent.png)

The generated hv line plot showed lowest gross rent for reported years was in Year [2010] amounting $(1239.0)
The average sale price per square foot dropped in 2011 approximately by $28 as compared to 2010 while the gross rent increased during this period..

![joined_chart](./Images/joined_plot.png)



### Compare the Average Sale Prices by Neighborhood

To create an interactive widget for neighborhood,the original sfo DataFrame was grouped by by year and neighborhood. The results were aggregated by the mean of the groups.

```
    # Group by year and neighborhood and then create a new dataframe of the mean values
    prices_by_year_by_neighborhood = sfo_data_df.groupby(["year","neighborhood"]).mean()

   # Filter out the housing_units
   prices_by_year_by_neighborhood = prices_by_year_by_neighborhood.drop(columns="housing_units")
   
   
   # Use hvplot to create an interactive line plot of the average price per square foot
    prices_by_year_by_neighborhood_plot=prices_by_year_by_neighborhood.hvplot(groupby="neighborhood",xlabel="Year",ylabel="Gross Rent/Sale Price Per Sqft",label="Sales Price Per Sqft & Gross Rent--San Fracisco 2010-2016-By Neighborhood",hover_color="yellow")

```

![neighborhood widget ](./Images/pricing-info-by-neighborhood.png)

### Build an Interactive Neighborhood Map

To build interactive map, the sfo_data_df DataFrame (created during the initial import), which included the neighborhood location data with the average prices was concatenated with neighborhood_locations_df (created from neighborhood_coordinates.csv). Firstly, original sfo data was grouped by neigborhood and then averaged the columns upon that. 

``` 
    ## Using the Pandas `concat` function, join the neighborhood_locations_df and the all_neighborhood_info_df DataFrame
    # The axis of the concatenation is "columns". 
    
    all_neighborhoods_df = pd.concat(
    [neighborhood_locations_df, all_neighborhood_info_df], 
    axis="columns",
    sort=False )
    
    # after cleaning and setting the "Neighborhood" columns, "all_neighborhoods_df" DataFrame was created for geospatial visualization.
    
    # Call the dropna function to remove any neighborhoods that do not have data
    all_neighborhoods_df = all_neighborhoods_df.reset_index().dropna()

    # Rename the "index" column as "Neighborhood" for use in the Visualization
    all_neighborhoods_df = all_neighborhoods_df.rename(columns={"index": "Neighborhood","Lat":"Latitude","Lon":"Longitude"})
    all_neighborhoods_df=all_neighborhoods_df.round(decimals=3)

``` 


Using hvPlot with GeoViews enabled, a points plot for the all_neighborhoods_df DataFrame is created. 


```
    all_neighborhoods_df = pd.concat(
    [neighborhood_locations_df, all_neighborhood_info_df], 
    axis="columns",
    sort=False )

    all_neighborhoods_plot=all_neighborhoods_df.hvplot.points( 'Longitude', 
    'Latitude', 
    geo=True,
    size='sale_price_sqr_foot',
    color='gross_rent',
    tiles='OSM',
    frame_width=700,
    frame_height=500,
    title='San Francisco Neighborhood Map')
all_neighborhoods_plot

```

![geomap](./Images/geoviews-plot.png)

The created data and visualizations provided an insight that during 2010-2016 housing units grew by approximately 2000 units per year while rental rates climbed more than 3 times. The Westwood Park had highest average rent but Union Square District was leading with highest sales price per sqft.

It is essential to consider that rent rates in San Francisco neighborhood does not necessarily correspond to sales price because there are areas that do not have comparatively high rent rates but their sales price per sqft are greater than areas with high rent rates. Additionally, the areas are spreaded out around the region rather than being focused in any single neighborhood.