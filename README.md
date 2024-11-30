# python-api-challenge

# WeatherPy

## Code to Generate Random Geographic Coordinates and a List of Cities

Dependencies and Setup
    
    import matplotlib.pyplot as plt
    
    import pandas as pd
    
    import numpy as np
    
    import requests
    
    import time
    
    from scipy.stats import linregress

Import the OpenWeatherMap API key

    from api_keys import weather_api_key

Import citipy to determine the cities based on latitude and longitude
    
    from citipy import citipy

# Generate the Cities List by Using the `citipy` Library 

Empty list for holding the latitude and longitude combinations

    lat_lngs = []

Empty list for holding the cities names
    
    cities = []

Range of latitudes and longitudes
    
    lat_range = (-90, 90)
    
    lng_range = (-180, 180)

Create a set of random lat and lng combinations
    
    lats = np.random.uniform(lat_range[0], lat_range[1], size=1500)
    
    lngs = np.random.uniform(lng_range[0], lng_range[1], size=1500)
    
    lat_lngs = zip(lats, lngs)

Identify nearest city for each lat, lng combination
    
    for lat_lng in lat_lngs:
        
        city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name
    
If the city is unique, then add it to a our cities list
        
        if city not in cities:
            
            cities.append(city)

Print the city count to confirm sufficient count
    
    print(f"Number of cities in the list: {len(cities)}")

![image](https://github.com/user-attachments/assets/fa750040-ee7b-4fb9-8b7d-58057b08be06)

# Requirement 1: Create Plots to Showcase the Relationship Between Weather Variables and Latitude

# Use the OpenWeatherMap API to retrieve weather data from the cities list generated in the started code

Set the API base URL
    
    url = "http://api.openweathermap.org/data/2.5/weather?"

Define an empty list to fetch the weather data for each city

    city_data = []

Print to logger
    
    print("Beginning Data Retrieval     ")
    
    print("-----------------------------")

Create counters
    
    record_count = 1
    
    set_count = 1

Loop through all the cities in our list to fetch weather data
    
    for i, city in enumerate(cities):

Group cities in sets of 50 for logging purposes
        
        if (i % 50 == 0 and i >= 50):
            
            set_count += 1
            
            record_count = 0

Create endpoint URL with each city
    
        city_url = f"{url}q={city}&appid={weather_api_key}&units=metric"

Log the url, record, and set numbers
    
        print(f"Processing Record {record_count} of Set {set_count} | {city}")

Add 1 to the record count
    
        record_count += 1

Run an API request for each of the cities
        
        try:

Parse the JSON and retrieve data
        
            city_weather = requests.get(city_url).json()

Parse out latitude, longitude, max temp, humidity, cloudiness, wind speed, country, and date
            
            city_lat = city_weather["coord"]["lat"]
            
            city_lng = city_weather["coord"]["lon"]
            
            city_max_temp = city_weather["main"]["temp_max"]
            
            city_humidity = city_weather["main"]["humidity"]
            
            city_clouds = city_weather["clouds"]["all"]
            
            city_wind = city_weather["wind"]["speed"]
            
            city_country = city_weather["sys"]["country"]
            
            city_date = city_weather["dt"]

Append the City information into city_data list
        
            city_data.append({"City": city,
                              
                              "Lat": city_lat,
                              
                              "Lng": city_lng,
                              
                              "Max Temp": city_max_temp,
                              
                              "Humidity": city_humidity,
                              
                              "Cloudiness": city_clouds,
                              
                              "Wind Speed": city_wind,
                              
                              "Country": city_country,
                              
                              "Date": city_date})

If an error is experienced, skip the city
        
        except:
            
            print("City not found. Skipping...")
            
            pass

pause to avoid rate limiting
    
        time.sleep(1)

Indicate that Data Loading is complete
    
    print("-----------------------------")
    
    print("Data Retrieval Complete      ")
    
    print("-----------------------------")

![image](https://github.com/user-attachments/assets/c4d4b07a-0b31-4cbc-b4b3-f9ad5484ea66)

Convert the cities weather data into a Pandas DataFrame

    city_data_df = pd.DataFrame(city_data)

Show Record Count

    city_data_df.count()

![image](https://github.com/user-attachments/assets/88b6c5ef-fb48-4382-8612-655049f81e8d)

Display sample data

    city_data_df.head()

![image](https://github.com/user-attachments/assets/1861c56c-17af-407c-8e85-afa437a55ff4)

Export the City_Data into a csv

    city_data_df.to_csv("output_data/cities.csv", index_label="City_ID")

Read saved data

        city_data_df = pd.read_csv("output_data/cities.csv", index_col="City_ID")
        
        Display sample data
        
        city_data_df.head()

![image](https://github.com/user-attachments/assets/4a161124-8c5c-4854-affb-618f06687669)

# Create the Scatter Plots Requested

# Latitude Vs. Temperature

Build scatter plot for latitude vs. temperature

    plt.scatter(city_data_df["Lat"], city_data_df["Max Temp"], color="lightblue", edgecolors="black")

Incorporate the other graph properties

    plt.title("City Max Latitude vs. Temperature (2024-11-29)")

    plt.xlabel("Latitude")

    plt.ylabel("Max Temperature (C)")

    plt.grid(True)

Save the figure

    plt.savefig("output_data/Fig1.png")

Show plot
    
    plt.show()

![image](https://github.com/user-attachments/assets/d8fda3f5-0e6f-4247-8274-a637a6688af3)

# Latitude Vs. Humidity

Build the scatter plots for latitude vs. humidity

    plt.scatter(city_data_df["Lat"], city_data_df["Humidity"], color="lightblue", edgecolors="black")

Incorporate the other graph properties
    
    plt.title("City Max Latitude vs. Humidity (2024-11-29)")
   
    plt.xlabel("Latitude")
    
    plt.ylabel("Humidity (%)")
    
    plt.grid(True)

Save the figure

    plt.savefig("output_data/Fig2.png")

Show plot
    
    plt.show()

![image](https://github.com/user-attachments/assets/52d61f57-7c4a-4daf-841a-878abafd7f94)

# Latitude Vs. Cloudiness

Build the scatter plots for latitude vs. cloudiness

    plt.scatter(city_data_df["Lat"], city_data_df["Cloudiness"], color="lightblue", edgecolors="black")

Incorporate the other graph properties
    
    plt.title("City Max Latitude vs. Cloudiness (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Cloudiness (%)")
    
    plt.grid(True)

Save the figure

    plt.savefig("output_data/Fig3.png")

Show plot

    plt.show()

![image](https://github.com/user-attachments/assets/d966b778-d42b-478b-b43b-527f3042ac39)

# Latitude vs. Wind Speed Plot

Build the scatter plots for latitude vs. wind speed

    plt.scatter(city_data_df["Lat"], city_data_df["Wind Speed"], color="lightblue", edgecolors="black")

Incorporate the other graph properties
    
    plt.title("City Max Latitude vs. Cloudiness (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Wind Speed (m/s)")
    
    plt.grid(True)

Save the figure
    
    plt.savefig("output_data/Fig4.png")

Show plot

    plt.show()

![image](https://github.com/user-attachments/assets/5c603059-e98a-4bfb-ba30-a80a1a713fd8)

# Requirement 2: Compute Linear Regression for Each Relationship

Define a function to create Linear Regression plots
    
    def compute_linear_regression(x_values, y_values):
    
        slope, intercept, r_value, p_value, std_err = linregress(x_values, y_values)
        
        print(f"The r^2 Value is: {r_value**2:}")
    
        return slope, intercept

Create a DataFrame with the Northern Hemisphere data (Latitude >= 0)

    northern_hemi_df = city_data_df[city_data_df["Lat"] >= 0]

Display sample data

    northern_hemi_df.head()

![image](https://github.com/user-attachments/assets/0c498687-7d4b-4ad4-9684-c02ee14d042d)

Create a DataFrame with the Southern Hemisphere data (Latitude < 0)

    southern_hemi_df = city_data_df[city_data_df["Lat"] <= 0]

Display sample data

    southern_hemi_df.head()

![image](https://github.com/user-attachments/assets/d9135bb2-a2ec-4ced-852d-6f1d914ce56d)

#  Temperature vs. Latitude Linear Regression Plot

Linear regression on Northern Hemisphere
    
    slope, intercept = compute_linear_regression(northern_hemi_df["Lat"], northern_hemi_df["Max Temp"])
    
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))
    
    
    plt.scatter(northern_hemi_df["Lat"], northern_hemi_df["Max Temp"], color="lightblue", edgecolors="black")
    
    plt.plot(northern_hemi_df["Lat"], slope * northern_hemi_df["Lat"] + intercept, color="red")
    
    plt.title("Latitude vs. Max Temp (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Max Temp")
    
    plt.annotate(line_eq,(10,-20),fontsize=15,color="red")
    
    plt.grid(True)
    
    plt.show()

![image](https://github.com/user-attachments/assets/e1840b80-3398-433e-889a-6a71b6cade8f)

Linear regression on Southern Hemisphere
    
    slope, intercept = compute_linear_regression(southern_hemi_df["Lat"], southern_hemi_df["Max Temp"])
    
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))

    
    
    plt.scatter(southern_hemi_df["Lat"], southern_hemi_df["Max Temp"], color="lightblue", edgecolors="black")
    
    plt.plot(southern_hemi_df["Lat"], slope * southern_hemi_df["Lat"] + intercept, color="red")
    
    plt.title("Latitude vs. Max Temp (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Max Temp")
    
    plt.annotate(line_eq,(-20,10),fontsize=15,color="red")
    
    plt.grid(True)
    
    plt.show()

![image](https://github.com/user-attachments/assets/35cad953-13a3-4381-a950-105e958ca109)

**Discussion about the linear relationship:** 

There is a strong correlation for both the Northern and Southern Hemisphere’s relationship between the latitude and max temperature.  The latitude in the Northern Hemisphere has its highest max temperatures around 0-30 which suggests that the closer to the equator, the higher the max temperature.  As we move further away from the equator the max temperature drops.  There is a similar correlation for the Southern Hemisphere, however the relationship is not as strong.  The Northern Hemisphere shows a much stronger relationship between latitude and max temperature as this is likely due to the greater amount of landmasses at higher latitudes.  The Southern Hemisphere has significantly more bodies of water which could be impacting the max temperature ranges and therefore resulting in the weaker correlation in our graph. 

![image](https://github.com/user-attachments/assets/d5eebee1-1109-4f55-bb23-4f828d882c25)

# Humidity vs. Latitude Linear Regression Plot

Northern Hemisphere
    
    slope, intercept = compute_linear_regression(northern_hemi_df["Lat"], northern_hemi_df["Humidity"])
    
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))

    
    
    plt.scatter(northern_hemi_df["Lat"], northern_hemi_df["Humidity"], color="lightblue", edgecolors="black")
    
    plt.plot(northern_hemi_df["Lat"], slope * northern_hemi_df["Lat"] + intercept, color="red")
    
    plt.title("Latitude vs. Humidity (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Humidity")
    
    plt.annotate(line_eq,(50,20),fontsize=15,color="red")
    
    plt.grid(True)
    
    plt.show()

![image](https://github.com/user-attachments/assets/9c81efc7-0546-4b19-a450-4ed78c0cd94f)

Southern Hemisphere

    slope, intercept = compute_linear_regression(southern_hemi_df["Lat"], southern_hemi_df["Humidity"])
    
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))

    
    
    plt.scatter(southern_hemi_df["Lat"], southern_hemi_df["Humidity"], color="lightblue", edgecolors="black")
    
    plt.plot(southern_hemi_df["Lat"], slope * southern_hemi_df["Lat"] + intercept, color="red")
    
    plt.title("Latitude vs. Humidity (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Humidity")
    
    plt.annotate(line_eq,(-50,5),fontsize=15,color="red")
    
    plt.grid(True)
    
    plt.show()

![image](https://github.com/user-attachments/assets/390da21a-310d-4fc7-b601-92ed241b5b3b)

**Discussion about the linear relationship:**

Both the Northern and Souther Hemispheres exhibit a weak correlations between latitude and humidity.  This indicaties that latitude may play some role in humidity but it is not a significant enough factor to confirm a definitive relationship between the two variables. According to the National Weather Services, Humidity levels are generally lower in winter than in summer, as such, variability is harder to determine during this particular period and may have influenced the correlation negatively.  If this graph was calculated during the summer, this graph may have reflected a much differe result. 

# Cloudiness vs. Latitude Linear Regression Plot

Northern Hemisphere

    slope, intercept = compute_linear_regression(northern_hemi_df["Lat"], northern_hemi_df["Cloudiness"])
    
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))
    
    
    plt.scatter(northern_hemi_df["Lat"], northern_hemi_df["Cloudiness"], color="lightblue", edgecolors="black")
    
    plt.plot(northern_hemi_df["Lat"], slope * northern_hemi_df["Lat"] + intercept, color="red")
    
    plt.title("Latitude vs. Cloudiness (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Cloudiness")
    
    plt.annotate(line_eq,(50,50),fontsize=15,color="red")
    
    plt.grid(True)
    
    plt.show()

![image](https://github.com/user-attachments/assets/7d8e0012-7a24-4bc7-b1af-206ec4c63b41)

Southern Hemisphere
    
    slope, intercept = compute_linear_regression(southern_hemi_df["Lat"], southern_hemi_df["Cloudiness"])
    
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))

    
    plt.scatter(southern_hemi_df["Lat"], southern_hemi_df["Cloudiness"], color="lightblue", edgecolors="black")
    
    plt.plot(southern_hemi_df["Lat"], slope * southern_hemi_df["Lat"] + intercept, color="red")
    
    plt.title("Latitude vs. Cloudiness (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Cloudiness")
    
    plt.annotate(line_eq,(-55,20),fontsize=15,color="red")
    
    plt.grid(True)
    
    plt.show()

![image](https://github.com/user-attachments/assets/5133d2de-d2a2-4092-8c54-2e04ffddf3df)

**Discussion about the linear relationship:**

Both hemispheres show a very weak relationship between latitude and cloudiness, as reflected by the low R^2 values. The weak correlation highlights that cloudiness is likely influenced by a  number of other factors, and latitude position is not a primary factor in the cloudiness of a particular region.  According to the Weather Channel, cloud cover is correlated with sunshine duration, so during the month on November into December, days become shorter (especially in the Northern Hemisphere) which would suggest why cloudiness is difficult to find a correlation during this periods. 

# Wind Speed vs. Latitude Linear Regression Plot

Northern Hemisphere
    
    slope, intercept = compute_linear_regression(northern_hemi_df["Lat"], northern_hemi_df["Wind Speed"])
    
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))

    
    plt.scatter(northern_hemi_df["Lat"], northern_hemi_df["Wind Speed"], color="lightblue", edgecolors="black")
    
    plt.plot(northern_hemi_df["Lat"], slope * northern_hemi_df["Lat"] + intercept, color="red")
    
    plt.title("Latitude vs. Wind Speed (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Wind Speed")
    
    plt.annotate(line_eq,(30,14),fontsize=15,color="red")
    
    plt.grid(True)
    
    plt.show()

![image](https://github.com/user-attachments/assets/2d4d790e-80ea-4ab5-8988-2f712464a23f)

Southern Hemisphere
  
    slope, intercept = compute_linear_regression(southern_hemi_df["Lat"], southern_hemi_df["Wind Speed"])
    
    line_eq = "y = " + str(round(slope,2)) + "x +" + str(round(intercept,2))
  
    
    plt.scatter(southern_hemi_df["Lat"], southern_hemi_df["Wind Speed"], color="lightblue", edgecolors="black")
    
    plt.plot(southern_hemi_df["Lat"], slope * southern_hemi_df["Lat"] + intercept, color="red")
    
    plt.title("Latitude vs. Wind Speed (2024-11-29)")
    
    plt.xlabel("Latitude")
    
    plt.ylabel("Wind Speed")
    
    plt.annotate(line_eq,(-30,10),fontsize=15,color="red")
    
    plt.grid(True)
    
    plt.show()

![image](https://github.com/user-attachments/assets/4e4c3465-70ae-4e47-9f40-dfe58438ca58)

**Discussion about the linear relationship:** 

The very low R^2 values in both hemispheres indicate that latitude does not have a strong impact on wind speed. According to the weather network, wind speed is influenced by many different factors, including atmospheric pressure systems, local geography and seasonal changes. In both the Northern and Southern Hemispheres, these factors have more significant impact of wind speed changes, making latitude a weak contributor of determining wind speed variability.  Stronger wind speed are present globally in April, suggesting the warm and cold tempretures clashing, these higher wind speeds could highlight stronger correlation between latitutde and wind speed, however, since the statiscs we are pulling are from a "traditionally" lower wind speed month (according to columbia university) these correlations may not be as evident.

# VacationPy

# Code to Import Libraries and Load the Weather and Coordinates Data

Dependencies and Setup
    
    import hvplot.pandas
    
    import pandas as pd
    
    import requests

Import API key

    from api_keys import geoapify_key

Load the CSV file created in Part 1 into a Pandas DataFrame

    city_data_df = pd.read_csv("output_data/cities.csv")

Display sample data

    city_data_df.head()

![image](https://github.com/user-attachments/assets/48fadb68-20d9-46a7-8f44-d1b9fd395e11)

# Step 1: Create a map that displays a point for every city in the `city_data_df` DataFrame. The size of the point should be the humidity in each city.

Configure the map plot
    
    map_plot = city_data_df.hvplot.points(
    
        'Lng',
        
        "Lat",
        
        geo = True,
        
        tiles = 'OSM',
        
        frame_width=1000,
        
        frame_height=600,
        
        size = 'Humidity',
        
        color = 'City')

Display the map

    map_plot

![image](https://github.com/user-attachments/assets/db54483b-efe4-4365-a76c-ad0a4d27c16b)

# Step 2: Narrow down the `city_data_df` DataFrame to find your ideal weather condition (I Like hot weather, minimal clouds, respectful amount of humidity and low wind)

Narrow down cities that fit criteria and drop any results with null values

Drop any rows with null values
    
    ideal_weather_df = city_data_df[
        
        (city_data_df['Max Temp'] > 22) & 
        
        (city_data_df['Humidity'] < 80) & 
        
        (city_data_df['Cloudiness'] < 40) & 
        
        (city_data_df['Wind Speed'] < 10)
    
    ].dropna()

Display sample data

    ideal_weather_df

![image](https://github.com/user-attachments/assets/f930182a-5a12-4e7e-baed-be0b307f97dc)

Replotted my ideal weather cities

    map_plot_2 = ideal_weather_df.hvplot.points(
    
        'Lng',
        
        "Lat",
        
        geo = True,
        
        tiles = 'OSM',
        
        frame_width=1000,
        
        frame_height=600,
        
        size = 'Humidity',
        
        color = 'City')
    
    map_plot_2

![image](https://github.com/user-attachments/assets/2f9f0157-b34f-466f-854b-e681411afcd2)

# Step 3: Create a new DataFrame called `hotel_df`.

Use the Pandas copy function to create DataFrame called hotel_df to store the city, country, coordinates, and humidity

    hotel_df = ideal_weather_df[['City', 'Country', 'Lat', 'Lng', 'Humidity']].copy()

Add an empty column, "Hotel Name," to the DataFrame so you can store the hotel found using the Geoapify API
    
    hotel_df['Hotel Name'] = ""

Display sample data
    
    hotel_df

![image](https://github.com/user-attachments/assets/e81a116f-856f-447f-b35f-f6c923a54cd9)

# Step 4: For each city, use the Geoapify API to find the first hotel located within 10,000 metres of your coordinates.

Set parameters to search for a hotel
    
    radius = 10000
    
    params = {
    
        "categories":"accommodation.hotel",
        
        "apiKey": geoapify_key,
        
        "limit":1
    
    }

Print a message to follow up the hotel search

    print("Starting hotel search")

Iterate through the hotel_df DataFrame

    for index, row in hotel_df.iterrows():

get latitude, longitude from the DataFrame
    
        lat = row['Lat']
        
        lng = row['Lng']

Add the current city's latitude and longitude to the params dictionary
        
        params["filter"] = f"circle:{lng},{lat},{radius}"
        
        params["bias"] = f"proximity:{lng},{lat}"

Set base URL

        base_url = "https://api.geoapify.com/v2/places"
 
Make and API request using the params dictionary

        response = requests.get(base_url, params=params)

Convert the API response to JSON format

        response = response.json()

Grab the first hotel from the results and store the name in the hotel_df DataFrame
        
        try:
        
            hotel_df.loc[index, "Hotel Name"] = response["features"][0]["properties"]["name"]
        
        except (KeyError, IndexError):

If no hotel is found, set the hotel name as "No hotel found".

            hotel_df.loc[index, "Hotel Name"] = "No hotel found"

Log the search results
    
        print(f"{hotel_df.loc[index, 'City']} - nearest hotel: {hotel_df.loc[index, 'Hotel Name']}")

Display sample data

    hotel_df

![image](https://github.com/user-attachments/assets/ec2aca3b-08d0-4faf-9d7c-e40f83e74c7e)

![image](https://github.com/user-attachments/assets/b9db1940-bfca-4435-a655-d706b6596a6e)

# Step 5: Add the hotel name and the country as additional information in the hover message for each city in the map.

Configure the map plot
    
    map_plot_3 = hotel_df.hvplot.points(
    
        'Lng',
        
        "Lat",
        
        geo = True,
        
        tiles = 'OSM',
        
        frame_width=1000,
        
        frame_height=600,
        
        size = 'Humidity',
        
        color = 'City',
        
        hover_cols=['City', 'Hotel Name', 'Country'])


Display the map
    
    map_plot_3

![image](https://github.com/user-attachments/assets/c555f092-863b-4eb3-bb5e-6e576e4320c8)
