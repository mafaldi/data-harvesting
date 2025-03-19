# Spanish airports information scraper and delays tracker

> Authors: Brád McKenzie & Mafalda González González

## 1. Description of the project

With this scraping project you will be able to scrape AENA, a Spanish website which holds information on all Spanish airports and their upcoming flights'. It uses a combination of Selenium scraping to manipulate the page search settings, plus traditional web scraping techniques to extract and clean the information.

**Key documents in this repository:**

|  |  |
|---------------------|---------------------------------------------------|
| Airport-scraper-full.Rmd | The big one! All your questions will be answered here. (... as long as your question is about the current status of Spanish airport departures) |
| Airport_code_search_table.csv | A file scraped with information on all AENA airports, their location and the IATA code. |
| Create airport code search table.Rmd | The scraping file to update the above code search csv. |
| Sample-plot-summary.Rmd | A short file you can apply to an output CSV file from the scraper to view the distribution of upcoming flights and get quick statistics on current airport status. |

## 2. Instructions

### 1: Setting up the Docker container

There is two options, you can try to **install Selenium**, and pray that it works with your computer, or you can **install Docker Desktop**. Docker Desktop will enable you to sideline the Selenium program and simplify the setup process by eliminating the need to manually install browsers, WebDriver binaries, and Selenium server dependencies. Hence, it is the option we went for and **recommend**.

If you do not have Docker Desktop, install the correct version for your system.

Once you have Docker, start it and run the **Docker command below** for the Selenium Firefox container in PowerShell (Windows) or Terminal (Apple).

```         
docker run -d -p 4445:4444 -p 5900:5900 --env VNC_NO_PASSWORD=1 --name selenium_firefox selenium/standalone-firefox-debug
```

This will open a **standalone container that will enable us to access webpages through Selenium in R**.

-   Optional: visible Firefox

    -   If you would like to see how Selenium works in action, you can install a VNC Viewer, such as TigerVNC to see the live browser.
    -   Once installed, connect the port to: localhost:5900. No password required

-   If you want to make sure that the container is running, you can type "docker ps" into your PowerShell / Terminal. You should be able to see the container selenium_firefox in the list.

## 2. Library requirements

Make sure you have the following libraries installed and loaded:

```         
library(RSelenium)
library(tidyverse)
library(rvest)
library(httr)
```

## 3. How to run this scraper?

Assuming Docker and your packages are installed, you can run the scraper.

### a) Access the scraping script

Download `Airport_scraper_full.Rmd` from this repository and open RStudio.

This code starts with all of the step by step code chunks to run the RSelenium code and process the table. However, most of these are set to `eval=FALSE` as we have more complete functions below that aggregate each step. We recommend looking through step-by-step to understand the process before running the functions.

Now, you can either search for 1 airport or all airports in Spain.

**In-scope flights**: Searches are set to return 6 hours of airport flight data. AENA generally includes flights from the 2 hours before and 4 hours ahead (or thereabouts). We select this 6 hour scope as delays are rarely announced over 6 hours in advance.

-   <div>

    **DEFAULT RUN SETTINGS**: If you run the Airport_scraper_full.Rmd in full, the default setting will be to loop through every single AENA airport to scrape every single html, extract the tables and clean them, then save a .csv for each airport and one full process. Depending on how many flights there are and how fast the system is running, this will take between 1 and 4 hours.

    </div>

#### [(i) To scrape information for one Spanish airport.]{.underline} 

2.  Choose a 3 digit AITA code for the airport you want to scrape. If you do not select one, you can still run the code. *The default search is MAD (Madrid–Barajas Airport).*

The file `Airport_code_search_table.csv` has information on all the AENA airports in Spain that can be searched. This table has been scraped from AENA and joined with Wikipedia information so the user can understand what city or region an airport is in. (If you think updates exist, you can use the `Create airport code search table.Rmd` to update and write a new csv file with the list of airports, codes and cities. This is fast to run. As at 19 March, there are 48 airports on AENA.)

3.  Go to the section **"Which code?"** at the top of the document and enter your airport code to the `airport_code <-` vector
4.  Go to the section **"Functionastic"** and we find the `scrape_flights()` function. You will see that this function calls all of the following functions too:
    -   *search_flights_aena()*

    -   *set_time_and_date()*

    -   *multi_click_viewmore()*
5.  *Optional:* If you want to save your output to a csv, use the *save_flights_data()* function.

**Expected time to run:** For large airports, this can take around 5 minutes. For small ones, it shouldn't take more than 1 minute. The longest extraction for small airports is connecting the Docker. The longest part for large airports is repeated page expansions through RSelenium to view all flights. If an airport has no information, it should return a message in the console to let you know.

**Airport cheat codes:** The IATA codes for the 3 busiest airports in Spain are: MAD (Madrid), BCN (Barcelona) and PMI (Palma De Mallorca). Each had over 30 million passengers in 2023.

#### [(ii) To scrape information for all airports in Spain (default)]{.underline}

2.  Go to the FAST AND FUNCTION section and run the `scrape_all_airports()` function.

-   This assumes you have preloaded all the dependent functions above. So it is best to run all code up til that function. Then run it individually if you wish to watch the console and a viewer while scraping.

-   This will loop through every airport code and return all of the html pages after RSelenium is applied to uptake the dynamic search bar.

This function loops through every single airport code retrieved by the `Create airport code search table.Rmd` file. The process loops through all of the IATA codes in order, and returns the html file for each airport.

3.  *Optional*: Save all files as a .csv

**Expected time to run:** To loop through all airports can take a few hours. With 48 airport codes and on average a couple of minutes per run, it can be anywhere from 1 hour to half a day.

## 3. Explanation of the script

Inside the script there will be a number of things happening:

1.  Rstudio will connect to the Selenium Firefox container

2.  Within that container, we will **load the [AENA website](https://www.aena.es/en/flight-info.html) and prepare it to be scraped**. This means a number of things using RSelenium, because all the search terms are java enabled and update the page code. :

    -   RSelenium is used to update all search terms from the default to view our desired in-scope flights. These are all set dynamically **so the data returned is live**. This scraper will always show current flight information when run, not historical. The updates include:
        -   *Searching for target airport*: select the departures box, enter the airport code and hit enter.
        -   *Set our 12 hour search window:* select the time box, read the current hour and minute settings, calculate 12 hours ahead as the end time, input the end hour and minute with the help of regex to standardise 1 vs 2 digit numeric values.
        -   *Set our calendar values:* select the calendar, set the end date based on whether it is today (e.g. 9am search that returns data until 9pm) or tomorrow (e.g. 4pm search that will go to 4am the next morning).
        -   *Scroll the page to show all in-scope flights:* select the "see more" flights and scroll the indefinitely until we reach the end of the page.
    -   Next, we read the HTML of the updated page search information to clean and extract the data.

3.  Having established a **static HTML**, the script will use with various **functions to prepare a dataset of the flights of the specific Airport** with general information such as their destination, the carriers or the gates, and with extra information on the delay status and time.

4.  Finally, it will **create a file**, `xxx`, that will have it all saved.

## 4. Known limitations

This scraper requires dynamic setting of times and dates through RSelenium that detect information on the page and the actual time to run data. There are two main timing issues:

-   *Running the scraper between midnight and 1:59am.* The system time will read date `today`. However, AENA search defaults to -2hrs before your page opens. So the AENA start date that we need to use will be set as date: `today - 1`. As the scraper is set to add 6 hours from the AENA date. It ends up adding 6 hours + 1 day to the search. So you will return 6 hours of extra flight information.

-   *Running the scraper after midday AND the last day of the month.* If you search in the afternoon, the scraper will update to set the end search time to early hours of the following morning. However, if this happens on the last day of the month, the scraper cannot update the calendar effectively to change the month and then search and select the first day of the following month.

## 5. Usages

This scraper produces a clean live dataset of the current status of any Spanish airport. Some suggested applications and research questions that may lead you to use this scraper:

1.  Check the current airport status For example; *what's happening at Valencia airport right now, are the flights on time? how many flights are there?*

    <div>

    To access some pre-prepared functions to apply to the output datasets, you can go to the `Sample functions to summarise and plot` .Rmd file. To run, update the airport_data \<- read_csv() function to the location of your dataset, then run the program.

    </div>

<!-- -->

2.  Build your own database to understand trends between and at airports. For example:

-   Create an automatic timer to scrape all the airport information at your time of interest and model the true airport delays over time at your airport of choice! Use cron or another similar program to schedule.

-   This could also be used to build a dataset to the frequency and duration of delays between different times of the day e.g. *is my flight out of Barcelona more likely to be delayed if I book in the morning or afternoon?*

3.  Update this scraping code to make your own search parameters. For example, if you want the search to be 12 hours, update the search terms within the time and calendar function to change your search times i.e. from `end_hr = start_hr +6` to `end_hr = start_hr +12`.
