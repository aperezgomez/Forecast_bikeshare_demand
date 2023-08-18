# Forecast_bikeshare_demand

---
title: "Forecast daily bike rental demand using time series models"
author: "Alexis Perez-Gomez"
date: "August, 2023"
output: word_document
---

# About Data Analysis Report

This RMarkdown file contains the report of the data analysis done for the project on forecasting daily bike rental demand using time series models in R. It contains analysis such as data exploration, summary statistics and building the time series models. The final report was completed on `r date()`.

**Data Description:**

This dataset contains the daily count of rental bike transactions between years 2011 and 2012 in Capital bikeshare system with the corresponding weather and seasonal information.

**Data Source:** <https://archive.ics.uci.edu/ml/datasets/bike+sharing+dataset>

**Relevant Paper:**

Fanaee-T, Hadi, and Gama, Joao. Event labeling combining ensemble detectors and background knowledge, Progress in Artificial Intelligence (2013): pp. 1-15, Springer Berlin Heidelberg

# Task One: Load and explore the data

## Install and load required packages

## Load data and install packages

```{r}
# Install and load required packages
install.packages("tidyverse")
install.packages("lubridate")
install.packages("plotly")
install.packages("forecast")

## Import required packages
library(tidyverse)
library(lubridate)
library(plotly)
library(forecast)

# Set ggplot theme
theme_set(theme_minimal())

## Load data (assuming the file is in your working directory)
bike_data_day <- read.csv("day.csv")
```

## Describe and explore the data

```{r}
# View the first few rows of the dataset
head(bike_data_day)

# Summary statistics
summary(bike_data_day)
```

![image](https://github.com/aperezgomez/Forecast_bikeshare_demand/assets/120771676/14ff8efc-cf7a-4226-b892-4d3ba551d5f6)

![image](https://github.com/aperezgomez/Forecast_bikeshare_demand/assets/120771676/786261db-e056-4bab-bf40-f23743b25c46)

# Task Two: Create interactive time series plots

```{r}
# Convert 'dteday' to Date class
bike_data_day$dteday <- as.Date(bike_data_day$dteday)

# Interactive time series plot for daily bike rentals
plot_ly(bike_data_day, x = ~dteday, y = ~cnt, type = 'scatter', mode = 'lines') %>%
  layout(title = "Daily Bike Rental Count Over Time", xaxis = list(title = "Date"), yaxis = list(title = "Bike Count"))
```

![image](https://github.com/aperezgomez/Forecast_bikeshare_demand/assets/120771676/4f232b7f-61b0-4358-88c6-623fe4b831cf)

```{r}
# Simple moving average
bike_data_day$SMA_30 <- zoo::rollmean(bike_data_day$cnt, k = 30, fill = NA)

ggplot(bike_data_day, aes(x = dteday)) +
  geom_line(aes(y = cnt), color = "blue") +
  geom_line(aes(y = SMA_30), color = "red") +
  labs(title = "30-Day Moving Average Vs. Actual Daily Counts", y = "Bike Count", x = "Date")
```

![image](https://github.com/aperezgomez/Forecast_bikeshare_demand/assets/120771676/f31ef85f-c5bf-42da-94dd-2fc104a9f276)

```{r}
time_series <- ts(bike_data_day$cnt, frequency = 365)
decomposed <- decompose(time_series)

autoplot(decomposed)

```

![image](https://github.com/aperezgomez/Forecast_bikeshare_demand/assets/120771676/2598b9ba-e0c3-4e0d-87de-da384725c773)

# Task Five: Fit and forecast time series data using ARIMA models

```{r}

# Fit ARIMA model
fit <- auto.arima(time_series)

# Forecast for next 30 days
forecasted <- forecast(fit, h = 30)

autoplot(forecasted) + labs(title = "ARIMA Forecast for Next 30 Days", x = "Date", y = "Bike Count")

```

![image](https://github.com/aperezgomez/Forecast_bikeshare_demand/assets/120771676/d1acd073-75ac-45d4-91c9-05100302514a)

# Task Six: Findings and Conclusions

Based on the analysis of the bike rentals dataset, the following conclusions can be drawn:

Trends Observed:

Upward Trend: The data clearly exhibits a consistent upward trend in bike rentals over the years. This indicates growing popularity and increased demand for bike rentals.

Seasonality: A prominent seasonal pattern is evident, with the summer months experiencing a peak in rentals. This peak in demand during the warmer months is likely due to favorable weather conditions, leading to an increase in outdoor activities.

Irregularities: The seasonal decline observed during off-peak seasons does not fall below the previous low within the same season. This suggests that while there are seasonal fluctuations, the overall growth is robust and consistent.

Model Findings:

The ARIMA model (AutoRegressive Integrated Moving Average) was employed to analyze and forecast the data. Without delving too deeply into the specifics of the model parameters (p, d, q) and (P, D, Q)s, the results affirm the presence of both seasonality and an upward trend in the data. This model is well-suited for time series data exhibiting such patterns.

The residuals of the model, which represent the difference between the observed values and the model's predictions, were generally small, indicating a good fit to the data.

Forecasts:

Based on the model's predictions, we expect the demand for bike rentals to continue growing in the upcoming months and years.

The summer months will likely continue to witness a surge in demand, given the consistent seasonal pattern observed in the past.

Although predictions are inherently accompanied by some uncertainty, the forecasted values, accompanied by their confidence intervals, provide a reasonable estimate for future performance.

In Summary: The analysis of the bike rentals dataset presents an optimistic view of the future for bike rentals. The consistent upward trend and clear seasonality patterns suggest that strategic planning and investment, especially ahead of peak seasons, could be beneficial. While it's crucial to be prepared for the peak seasons, the overall growth in demand suggests that focusing on year-round strategies and offerings might also be fruitful.






