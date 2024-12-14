---
params:
  date_from: "2022-01-23"
  date_until: "2022-07-23"
  country: "Netherlands"
---
# Parameters



In this page we will look at the use of parameters in Rmarkdown. To this end we will use the data from the European Center of Disease Control COVID-19 case data. The goal is to create two separate graphs, one showing the COVID-19 related cases in a specified month of a year in a country and the other the COVID-19 related deaths in a specified month of a year in a country. To this end we will make the parameters customisable to the month, year and country the graphs are supposed to show.

Parameters are defined in the yaml header of Rmarkdown documents and can be used just like any variable in the code. One great advantage is that they can be customized on the fly to change function output. In this case we defined three parameters: 

| Paramater Name  | Param Input   |
| --------------- | ------------- |
|  date_from:     |"2022-01-23"   |
|  date_until:    |"2022-07-23"   |
|  country:       |"Netherlands"  |

  
We can change them in to any date combinations we want and any country as long as it has data in our input data the code will work. This makes it work as-though we wrote a function of the code with custom input but then our function is the complete Rmarkdown file. Very handy indeed.

This page will show all the code so you can take a look at how the parameters are used in it. You can find the parameters used as variables: params$parameter_name

``` r
###################### Load in the Data ########################################

## First lets load in the Data kindly supplied by the ECDC. 
  data <- read_csv(here::here("data/covid_data.csv"))

######################## Data wrangling ########################################
## Now we are going to change the date data type to date instead of chr.
  data$dateRep <- as.Date(data$dateRep, "%d/%m/%Y")

############################# Graphs ###########################################

## Because the data is already tidy enough we don't need to wrangle further and can simply start plotting the graphs we want.
  
# Graph #1, the amount of covid-19 cases in a selected country for a selected period of time
  data %>% select(month, year, day, cases, countriesAndTerritories, dateRep) %>% 
    filter(dateRep >= as.Date(params$date_from) & dateRep <= as.Date(params$date_until), countriesAndTerritories %in% c(params$country)) %>%
    ggplot(aes(x=dateRep, y=cases)) + 
    geom_line(aes(color = countriesAndTerritories)) +
    labs(title= paste("Covid-19 Cases in", params$country),
         subtitle = paste(params$date_from, params$date_until),
         x= "Date",
         y= "Number of Cases") +
    scale_color_manual(values=c("turquoise3")) +
    theme_classic()
```

<img src="07-parameters_files/figure-html/unnamed-chunk-2-1.png" width="672" />

``` r

# Graph #2, The amount of covid-19 Deaths in a selected country for a selected period of time. If I didn't work with parameters I would have build a function for these two graphs seeing as they are very similar in code. 
  data %>% select(month, year, day, deaths, countriesAndTerritories, dateRep) %>% 
    filter(dateRep >= as.Date(params$date_from) & dateRep <= as.Date(params$date_until), countriesAndTerritories %in% c(params$country)) %>%
    ggplot(aes(x=dateRep, y=deaths)) + 
    geom_line(aes(color = countriesAndTerritories)) +
    labs(title= paste("Covid-19 Deaths in", params$country),
         subtitle = paste(params$date_from, params$date_until),
         x= "Date",
         y= "Number of Deaths") +
    scale_color_manual(values="turquoise4") +
    theme_classic()
```

<img src="07-parameters_files/figure-html/unnamed-chunk-2-2.png" width="672" />

Parameters are great to work with and save time when you want to only change small things about code that has already been used before. I personally prefer writing good functions and reusing them but parameters are a great tool to apply aswell. 
