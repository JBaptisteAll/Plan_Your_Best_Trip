# **Weather Insights for Your Next Adventure** 🌍

## **Introduction**
Planning a trip can be tricky without the right data. This project aims to simplify that process by analyzing **weather forecasts for the next five days** across 35 destinations. With real-time **GPS data** and advanced **weather analytics**, this project provides clear insights into weather conditions to help users make informed travel decisions.

This project demonstrates expertise in **data acquisition**, **API integration**, and **data visualization**. By leveraging real-time datasets and advanced analytical techniques, I developed a solution to **analyze** and **visualize** weather trends effectively, delivering actionable insights for better decision-making.


---

## **Problem Statement** 🧐

When planning a trip, 70% of users express a desire for more detailed and reliable information about their travel destinations. However, they often face several challenges:

- **Trust issues**: Many users struggle with trusting information from unfamiliar sources.
- **Data overload**: The overwhelming amount of unstructured information online makes it difficult to identify actionable insights.
- **Fragmented tools**: Most travel platforms do not integrate real-time weather insights, leaving users with incomplete data.
- **Accuracy concerns**: Inconsistent or outdated weather data often leads to uncertainty, making it harder for users to plan effectively.

This highlights the need for a data-driven solution that consolidates reliable weather information, empowering users to make informed decisions with confidence.


---

## **Project Goals** 🎯
1. **Data Acquisition**:
   - Retrieve GPS coordinates for 35 destinations.
   - Integrate real-time weather data using APIs.
2. **Data Analysis**:
   - Classify and rank destinations based on weather conditions.
   - Visualize weather trends to provide actionable insights.

---

## **Tools & Technologies** 🛠️
### **Development Environment**
- **Visual Studio Code (VSCode):** Used as my primary development environment for coding, debugging, and executing Python scripts.

### **Programming & Libraries**
- **Python:** Core language for this project, enabling efficient data manipulation and API interactions.
- **Pandas:** Used to clean, transform, and structure datasets into DataFrames for analysis.
- **Plotly Express:** A powerful library for creating interactive visualizations like the dynamic map included in this project.

### **Version Control**
- **Git:** Used for version control to track changes throughout the development process.
- **GitHub:** Essential for hosting the project, enabling collaboration, and sharing the code with others.

---

## **Methodology** 🚀
1. **GPS Data Collection**:
   - Using the API of [Nominatim](https://nominatim.openstreetmap.org/search), GPS coordinates were fetched for 35 destinations across the globe.
   - These coordinates were used as input for the weather data acquisition in the next step.

| City                | Latitude   | Longitude         |
|---------------------|------------|-------------------|
| Mont Saint Michel   | 48.6359541 | -1.511459954959514|
| St Malo             | 48.649518  | -2.0260409        |
| Bayeux              | 49.2764624 | -0.7024738        |
| Le Havre            | 49.4938975 | 0.1079732         |
| Rouen               | 49.4404591 | 1.0939658         |
| Paris               | 48.8534951 | 2.3483915         |
| Amiens              | 49.8941708 | 2.2956951         |
| Lille               | 50.6365654 | 3.0635282         |
| Strasbourg          | 48.584614  | 7.7507127         |
| Annecy              | 45.8992348 | 6.1288847         |

2. **Weather Data Integration**:
   - Weather forecasts for the next five days were retrieved using the API of [OpenWeatherMap](https://api.openweathermap.org/data/2.5/forecast).  
   - The API responses, formatted as JSON, were processed and structured into a DataFrame for analysis.

|   | City              | Latitude   | Longitude         | Date       | Temp_Max | Temp_Min | Humidity | Weather         |
|---|-------------------|------------|-------------------|------------|----------|----------|----------|-----------------|
| 0 | Mont Saint Michel | 48.6359541 | -1.511459954959514 | 2024-11-16 | 9.08     | 6.89     | 79       | clear sky       |
| 1 | Mont Saint Michel | 48.6359541 | -1.511459954959514 | 2024-11-16 | 9.29     | 8.85     | 75       | scattered clouds |
| 2 | Mont Saint Michel | 48.6359541 | -1.511459954959514 | 2024-11-17 | 9.51     | 9.51     | 75       | broken clouds   |
| 3 | Mont Saint Michel | 48.6359541 | -1.511459954959514 | 2024-11-17 | 10.10    | 10.10    | 75       | overcast clouds |
| 4 | Mont Saint Michel | 48.6359541 | -1.511459954959514 | 2024-11-17 | 10.62    | 10.62    | 74       | overcast clouds |


   - A new column, **Average Temperature**, was calculated for each row as the mean of the minimum and maximum temperatures.  

```python
df_meteo["Temp_Avg"] = (df_meteo["Temp_Max"] + df_meteo["Temp_Min"]) / 2
```

   - The data included:  
     - **Temperature** (minimum, maximum, and calculated average values).  
     - **Humidity** (indicating the comfort level of the weather).  
     - **Weather Descriptions** (e.g., clear sky, rain, snow).  

   - A custom **Weather Score** was calculated to rank the destinations based on their favorability. This score was derived using the following rules:  
     - Clear skies and warm temperatures contributed positively to the score.  
     - Rainy or snowy conditions reduced the score, with heavier precipitation having a greater negative impact.  
     - Extreme weather conditions, such as storms, were heavily penalized.  
     - The formula incorporated weighted averages of temperature, humidity, and weather conditions to provide a holistic assessment of weather favorability.  

```python
# Dictionary for the quotation of weather condition, good condition get positive number
# and bad condition negative.
notations = {
    "clear sky": 600,
    "few clouds": 500,
    "scattered clouds": 400,
    "broken clouds": 300,
    "overcast clouds": 200,    

    "light intensity drizzle": -1,
    "drizzle": -2,
    "heavy intensity drizzle": -3,
    "light intensity drizzle rain": -4,
    "drizzle rain": -5,
    "heavy intensity drizzle rain": -6,
    "shower drizzle": -7,
    "shower rain and drizzle": -8,
    "heavy shower rain and drizzle": -9,    

    "light rain": -10,
    "moderate rain": -20,
    "heavy intensity rain": -30,
    "very heavy rain": -40,
    "extreme rain": -50,
    "freezing rain": -60,
    "light intensity shower rain": -70,
    "shower rain": -80,
    "heavy intensity shower rain": -90,
    "ragged shower rain": -100,

    "thunderstorm with light drizzle": -15,
    "thunderstorm with drizzle": -25,
    "thunderstorm with light rain": -35,
    "thunderstorm with rain": -45,
    "thunderstorm with heavy drizzle": -55,
    "thunderstorm with heavy rain": -65,
    "thunderstorm": -75,
    "heavy thunderstorm": -85,
    "ragged thunderstorm": -95,

    "light snow": -20,
    "snow": -40,
    "heavy snow": -60,
    "sleet": -80,
    "light shower sleet": -100,
    "shower sleet": -120,
    "light rain and snow": -140,
    "rain and snow": -160,
    "light shower snow": -180,
    "shower snow": -200,
    "heavy shower snow": -220,

    "mist": -10,
    "smoke": -20,
    "haze": -30,
    "sand/dust whirls": -40,
    "fog": -50,
    "sand": -60,
    "dust": -70,
    "volcanic ash": -90,
    "squalls": -100,
    "tornado": -400,
}
```
```python
df_meteo["Weather_Score"] = df_meteo["Weather"].map(notations)
```
```python
# Total "Weather Score" points per city
total_points_par_ville = df_meteo.groupby("City")["Weather_Score"].sum().reset_index()
total_points_par_ville = total_points_par_ville.sort_values(by="Weather_Score", ascending=False)

display(total_points_par_ville)
```

3. **Data Analysis**:
   - The data was cleaned and structured into a unified DataFrame for easier interpretation.

   - Destinations were ranked and compared based on:
     - Average temperature.
     - Weather score trends over five days.
     - Anomalies like extreme weather conditions.

| City                  | 2024-11-16 Temp_Avg | 2024-11-16 Weather_Score | 2024-11-17 Temp_Avg | 2024-11-17 Weather_Score | 2024-11-18 Temp_Avg | 2024-11-18 Weather_Score | 2024-11-19 Temp_Avg | 2024-11-19 Weather_Score | 2024-11-20 Temp_Avg | 2024-11-20 Weather_Score | 2024-11-21 Temp_Avg | 2024-11-21 Weather_Score |
|-----------------------|---------------------|---------------------------|---------------------|---------------------------|---------------------|---------------------------|---------------------|---------------------------|---------------------|---------------------------|---------------------|---------------------------|
| Aigues Mortes         | 12.00              | 800                       | 12.99              | 3000                      | 12.20              | 2800                      | 13.03              | 2100                      | 11.86              | 1680                      | 5.62               | 970                       |
| Aix en Provence       | 12.75              | -20                       | 12.79              | 2900                      | 10.44              | 2400                      | 10.43              | 2500                      | 9.54               | 2080                      | 3.58               | 1780                      |
| Amiens                | 8.13               | 700                       | 8.56               | 1180                      | 9.20               | 330                       | 8.60               | -110                      | 3.86               | 920                       | 1.62               | 600                       |
| Annecy                | 6.80               | 900                       | 7.20               | 3200                      | 5.76               | 2300                      | 6.19               | 320                       | 3.26               | -240                      | -1.94              | 800                       |
| Ariege                | 13.55              | 700                       | 13.00              | 2800                      | 11.17              | 1800                      | 11.15              | 2200                      | 6.27               | 490                       | 7.66               | 1400                      |
| Avignon               | 11.61              | 400                       | 11.35              | 2600                      | 10.76              | 2300                      | 10.35              | 1800                      | 9.84               | 2080                      | 3.33               | 1470                      |
| Bayeux                | 9.81               | 500                       | 9.59               | 1180                      | 10.42              | 330                       | 9.64               | -110                      | 5.49               | 1030                      | 3.95               | -110                      |
| Bayonne               | 15.00              | 500                       | 13.25              | 1790                      | 12.45              | 1600                      | 13.81              | 650                       | 11.17              | 760                       | 11.59              | 570                       |
| Besancon              | 5.24               | 800                       | 5.46               | 2000                      | 7.15               | 1600                      | 8.54               | 310                       | 2.99               | 200                       | 0.40               | 930                       |
| Biarritz              | 15.47              | 500                       | 13.70              | 1370                      | 12.74              | 1600                      | 14.12              | 650                       | 11.44              | 340                       | 12.22              | 570                       |
| Bormes les Mimosas    | 13.60              | 600                       | 14.17              | 3300                      | 12.39              | 2500                      | 11.75              | 2800                      | 10.80              | 1880                      | 5.93               | 1580                      |
| Carcassonne           | 11.00              | 1000                      | 12.64              | 2800                      | 10.96              | 2300                      | 11.04              | 2000                      | 9.33               | 1770                      | 7.19               | 1090                      |

*Only a subset of rows is displayed for clarity.*


### **Interactive Weather Map**
This project features an **animated density map** that visualizes temperature averages across destinations over five days. 

Below is a preview of the interactive map:
```python
df_meteo["Latitude"] = pd.to_numeric(df_meteo["Latitude"], errors="coerce")
df_meteo["Longitude"] = pd.to_numeric(df_meteo["Longitude"], errors="coerce")

df_meteo_pos = df_meteo[df_meteo["Weather_Score"] >= 0]
```
```python
fig = px.density_mapbox(
    df_meteo_pos,
    lat="Latitude",
    lon="Longitude",
    z="Temp_Avg",
    mapbox_style="open-street-map",
    animation_frame="Date",
    zoom=3.5,
    radius=10,
    center={"lat": 46.603354, "lon": 1.888334}
)

fig.show()
```

![Interactive Weather Map](Plan_Your_Best_Trip\weather_map.png)

#### **Features of the Map**:
- **Color intensity**: Reflects average temperatures for each destination.
- **Animation**: Shows how weather conditions evolve over the five-day period.
- **Interactive exploration**: Allows zooming, panning, and hovering for detailed insights.

---

## **Key Results** 📊

### **Sample Data Analysis**
Here is a snapshot of the processed data:

### Paris Weather 🌤️

Below is a preview of the dataset used for weather analysis in Paris:

|   | City   | Latitude   | Longitude  | Date       | Temp_Max | Temp_Min | Humidity | Weather          |
|---|--------|------------|------------|------------|----------|----------|----------|------------------|
| 200 | Paris  | 48.8534951 | 2.3483915 | 2024-11-16 | 10.48    | 9.35     | 70       | broken clouds    |
| 201 | Paris  | 48.8534951 | 2.3483915 | 2024-11-16 | 9.28     | 9.13     | 67       | broken clouds    |
| 202 | Paris  | 48.8534951 | 2.3483915 | 2024-11-16 | 8.45     | 8.00     | 70       | scattered clouds |
| 203 | Paris  | 48.8534951 | 2.3483915 | 2024-11-17 | 7.10     | 7.10     | 75       | few clouds       |
| 204 | Paris  | 48.8534951 | 2.3483915 | 2024-11-17 | 7.98     | 7.98     | 73       | broken clouds    |
| 205 | Paris  | 48.8534951 | 2.3483915 | 2024-11-17 | 8.41     | 8.41     | 72       | overcast clouds  |

*Note: Only a subset of rows is displayed for clarity.*

---
### Top 10 Cities by Weather Score 🌟

Below is the list of the top 10 cities ranked by their weather scores, highlighting the destinations with the most favorable weather conditions:

| Rank | City                 | Weather_Score |
|------|----------------------|---------------|
| 1    | Bormes les Mimosas   | 12660         |
| 2    | Gorges du Verdon     | 12430         |
| 3    | Cassis               | 11840         |
| 4    | Aix en Provence      | 11640         |
| 5    | Marseille            | 11620         |
| 6    | Aigues Mortes        | 11350         |
| 7    | Toulouse             | 11250         |
| 8    | Collioure            | 11190         |
| 9    | Carcassonne          | 10960         |
| 10   | Nimes                | 10840         |

---

This top 10 list showcases the most desirable travel destinations based on weather conditions, offering insights for travelers seeking the best climates.

---

### Closing Thoughts

This project demonstrated my **data analysis skills**, including the use of **Python** and libraries like **Pandas** and **Plotly**. By integrating GPS and weather data, I developed an intuitive tool to analyze and rank destinations, showcasing expertise in **data acquisition**, **processing**, and **visualization**.

This experience highlights the importance of mastering key tools while focusing on user-centric solutions. Continuous learning remains essential for success in the evolving field of data analytics.


---
*Additional insights and full results are available in the project files.*