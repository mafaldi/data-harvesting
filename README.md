# Spanish Airports information scraper and delays tracker

> Authors: Brád McKenzie & Mafalda González González

## 1. Description of the project

With this scraping project you will be able to scrape AENA, a Spanish website which holds information on all Spanish Airports and their upcoming flights'. It uses a combination of Selenium scraping to manipulate the page search settings, plus traditional web scraping techniques to extract and clean the information.

In the

**Key documents in this repository:**

| Document | Description |
|------------------------------------|------------------------------------|
| Airport-scraper-full.Rmd | The big one! All your questions will be answered here (... as long as your question is about the current status of Spanish Airport departures). |
| Sample-plot-summary.Rmd | A short file you can apply to an output CSV file from the scraper to view the distribution of upcoming flights and get quick statistics on current airport status. |

## 2. Instructions 

There are three steps to this scraper. First, you will have to (i) set up your docker container, then you will have to make sure that (ii) all libraries are installed, and finally, you will (iii) run the scraper, for which there are two options:

a) The first option, [Shortcut it]{.underline}, gives you the possibility to run the whole scraper from top to bottom.

b) The second option, [Scraping script step by step]{.underline}, gives you a step by step guide on how the code scrapes the websites and stores the information. While the .Rmd script has a commentary and descriptions so that you can follow it like a tutorial, the step by step guide of this README is a handy summary of what will be happening. Furthermore, it gives you the option to try the code out either for one Airport, or to scrape all Airports.

### (i) Setting up the Docker container

There is two options, you can try to install Selenium, and pray that it works with your computer, or you can **install Docker Desktop**. Docker Desktop will enable you to sideline the Selenium program and simplify the setup process by eliminating the need to manually install browsers, WebDriver binaries, and Selenium server dependencies. Hence, it is the option we went for and **recommend**.

1.  If you do not have Docker Desktop, install the correct version for your system.

2.  Once you have Docker, start it and run the **Docker command below** for the Selenium Firefox container in PowerShell (Windows) or Terminal (Apple). This will open a [standalone container that will enable us to access webpages through Selenium in R]{.underline}.

    ```         
    docker run -d -p 4445:4444 -p 5900:5900 --env VNC_NO_PASSWORD=1 --name selenium_firefox selenium/standalone-firefox-debug
    ```

3.  Optional: visible Firefox

    -   If you would like to see how Selenium works in action, you can install a VNC Viewer, such as TigerVNC to see the live browser.
    -   Once installed, connect the port to: localhost:5900. No password required

4.  If you want to make sure that the container is running, you can insert the code below into your PowerShell / Terminal. You should be able to see the container selenium_firefox in the list.

    ```         
    docker ps
    ```

### (ii) Library requirements

Make sure you have the following libraries installed and loaded:

```{r, eval=F}
# Selenium
library(RSelenium)
library(tidyverse)
library(rvest)
library(httr)

# List of Airports
library(xml2)
library(janitor)
library(readr)

# Nodes
library(httr2)
library(dplyr)
library(stringr)
library(purrr)
library(lubridate)
```

testtest

```         
# Selenium
library(RSelenium)
library(tidyverse)
library(rvest)
library(httr)

# List of Airports
library(xml2)
library(janitor)
library(readr)

# Nodes
library(httr2)
library(dplyr)
library(stringr)
library(purrr)
library(lubridate)
```

## (iii) How to run this scraper?

Assuming Docker and your packages are installed, you are now ready to run the scraper. You have two options:

### a) Shortcut it

> This option is for if you just want the code.

1.  Download `Airport_scraper_full.Rmd` from this repository
2.  Open RStudio and open the `Rmd` file
3.  Go to the section `Set your user-agent` in which you can introduce your user-agent to ethically scrape the web. The section is at the very beginning, so you can't miss it.
4.  Run the whole code from top to bottom. And thats it, and yes, its that easy! To run the whole code either:
    -   Click **"**Run**" → "**Run All**"** in the R Markdown toolbar
    -   Use the shortcut:
        -   `Ctrl + Alt + R` (Windows/Linux)
        -   `Cmd + Option + R` (Mac)

🌟🌟🌟 **Output**: Once the script has finished, you will have a new folder, flights_data in where all the CSV files are saved, including the master file `all_flights_combined.csv`, which has the information of all Airports in a single data frame.

**Why this works**: Any explanatory or demonstrative code is put to `eval=F` so that you dont have to worry about it.

**Disclaimer**: Once you are running the code, you will be able to see your progress in the console through messages we have integrated into our code. Now, sit back, relax and wait... for probably a **long time**:

<div>

⚠️❗**DEFAULT RUN SETTINGS**: If you run the `Airport_scraper_full.Rmd` in full, the default setting will be to loop through every single AENA Airport to scrape every single html, extract the tables and clean them, then save a .csv for each Airport and one full process.

-   Some Airports like Madrid and Barcelona have many flights with multiple companies carrying them, so the number of flights in a short amount of time is exponential.
-   In addition, the code has integrated sleeper to both not to scrape too much at once and give the websites time to load. This adds its weight to the run time.

Depending on how many flights there are, at what point of the day you are scraping and how fast the system is running, this will take between 1 and half a day.

</div>

------------------------------------------------------------------------

### b) Scraping script step by step 

> This option is for you to explore the code and its intricacies.

1.  Download `Airport_scraper_full.Rmd` from this repository
2.  Open RStudio and open the `Rmd` file
3.  Go to the section ***First Steps***

Now, set your user-agent for ethical scraping. Follow also the instructions of the subsections *Connect to Docker*, through which Rstudio will connect to the Selenium Firefox container, *Load packages* and *List of Airports* within First Steps. This way, you will be able to not only run the whole code but also individual code chunks for you tó experiment and test the code.

4.  Explore the *RSelenium scraping of AENA searchbar* section if you want to read how the Selenium commands work and why we use them.

In this section you will have a step by step rundown of the RSelenium code. Most of the code is set to `eval=FALSE` as their main purpose is explanation and demonstration. We have complete functions below that aggregate each step. We recommend looking through step-by-step to understand the process before running the functions.

5.  Run all subsections of ***FUNctionastic***. This is imperative! This section has all the functions that automate the Selenium code.

In *FUNctionastic* you will find the following functions: `search_flights_aena()`, `set_time_and_date()`, `check_flights_exist()`, `multi_click_viewmore()` and `save_airport_html()`, as well as the master function that call on all the previous functions, `scrape_all_airports()`, which is in the subsection *FAST AND FUNCTION*. If you want to know what each function does, you can check out step 4 of this guide.

6.  🌟Go to next section, ***Selenium scraping***, to either scrape the HTML of 1 Airport or the HTMLs of all Airports in Spain.

To scrape 1 Airport or all Airports in Spain, simply follow the respective subsection of *Selenium scraping* and our instructions below, i.e. **(i)** *Selenium scrape one Airport* or **(ii)** *Selenium scrape all Airports*.

⚠️❗**In-scope flights**: scrape searches are set to return 6 hours of Airport flight data. AENA generally includes flights from the 2 hours before and 4 hours ahead (or thereabouts). We select this 6 hour scope as delays are rarely announced over 6 hours in advance.

#### [(i) To scrape the HTML for one Spanish Airport]{.underline}

To scrape only one Spanish Airport you must choose a 3 digit AITA code of the Airport of your choice. The choices were already scraped, processes and loaded in *List of Airports* at the very beginning. Now, in the subsection ***Selenium scrape one Airport*** you can simply load the dataset `full_airport_names` and insert your desired Airport into the function `scrape_all_airports()` as per the instructions given in the subsection we are in.

7.  🌟🌟**Tibble it**: to extract the information of the HTML into a tibble, run the entirety of ***Nodes scraping***. The output will appear in the section ***Node scrape one Airport***. If it doesn't work, make sure you loaded the entire chunk in the *Selenium scrape one Airport* section.
8.  🌟🌟🌟**CSV**: to save your output to a CSV, load `save_flights_data()` in the section ***Individual Airports CSV function*** and then follow the instructions of the subsection ***One Airport CSV***.

**Expected time to run:** For large Airports, this can take around 5 minutes. For small ones, it shouldn't take more than 1 minute.

-   The slowest part for small Airports is connecting the Docker.
-   The longest part for large Airports is repeated page expansions through RSelenium to view all flights.
-   If an Airport has no information, it should return a message in the console to let you know.

**Airport cheat codes:** The IATA codes for the 3 busiest Airports in Spain are: MAD (Madrid), BCN (Barcelona) and PMI (Palma De Mallorca). Each had over 30 million passengers in 2023.

#### [(ii) To scrape information for all Airports in Spain (default)]{.underline}

To scrape all Airports' HTMLs proceed to the subsection ***Selenium scrape all Airports*** and [load the whole chunk]{.underline}. The function utilizes the Airport codes we retrieved in *List of Airports* to create a list, `airport_codes`. With this list we can call the commands to loop through every Airport IATA code in order. The profess will create a folder, `flight_htmls`, store inside it a HTML of each Airport website (after `RSelenium` is applied to uptake the dynamic search bar) and create a list with the HTML content of each file paired with the corresponding Airport code.

7.  🌟🌟**Tibble it** and **save the CSVs**: to extract the information of the HTMLs into a tibble and save it as a CSV file, run the entirety of the ***Nodes scraping*** section. Then, load the entire chunk of the `save_flights_data()` function in the section ***Individual Airports CSV***. Having done that, skip over to the subsection ***LOOP for all individual Airports CSVs*** and load the chunk.
    -   ⚠️❗It is not possible to only extract the information into a tibble without creating a CSV!
8.  🌟🌟🌟 **Crreate one aggregated dataset**: to create one dataset that contains all information of all datasets, head to the last section, ***One dataset***, and run the code in its entirety. It has the functions `combine_flights_data()`, which reads all the CSVs available and compiles their information into a single dataframe while inserting the source Airport, and `save_combined_flights()`, which build upon the previous function to save the new CSV.

**Expected time to run:** To loop through all Airports can take a few hours. With 48 Airport codes and on average a couple of minutes per run, it can be anywhere from 1 hour to half a day.

<div>

⚠️❗**DEFAULT RUN SETTINGS**: If you run the `Airport_scraper_full.Rmd` in full, the default setting will be to loop through every single AENA Airport to scrape every single html, extract the tables and clean them, then save a .csv for each Airport and one full process.

-   Some Airports like Madrid and Barcelona have many flights with multiple companies carrying them, so the number of flights in a short amount of time is exponential.
-   In addition, the code has integrated sleeper to both not to scrape too much at once and give the websites time to load. This adds its weight to the run time.

Depending on how many flights there are, at what point of the day you are scraping and how fast the system is running, this will take between 1 and half a day.

</div>

## 3. Explanation of the script

Inside the script there will be a number of things happening. Lets take the case of a single Airport:

1.  Rstudio will connect to the Selenium Firefox container

2.  Within that container, we will **load the [AENA website](https://www.aena.es/en/flight-info.html) and prepare it to be scraped**. This means a number of things using RSelenium, because all the search terms are java enabled and update the page code:

    -   RSelenium is used to update all search terms from the default to view our desired in-scope flights. These are all set dynamically **so the data returned is live**. This scraper will always show current flight information when run, not historical. The updates include:
        -   *Searching for target Airport*: select the departures box, enter the Airport code and hit enter.
        -   *Set our 6 hour search window:* select the time box, read the current hour and minute settings, calculate 6 hours ahead as the end time, input the end hour and minute with the help of regex to standardise 1 vs 2 digit numeric values.
        -   *Set our calendar values:* select the calendar, set the end date based on whether it is today (e.g. 9am search that returns data until 9pm) or tomorrow (e.g. 4pm search that will go to 4am the next morning).
        -   *Scroll the page to show all in-scope flights:* select the "see more" flights and scroll the indefinitely until we reach the end of the page.
    -   Next, we read the HTML of the updated page search information to clean and extract the data.

3.  Having established **static HTML**, the script will use with various **functions to prepare a dataset of the flights of the specific Airport** with general information such as their destination, the carriers or the gates, and with extra information on the delay status and time.:

    -   *Clean the information and create a tibble*: select the rows with each flight out of the static HTML, separate and catgeorise their information into values, read the flight times with regex so to establish current and delayed flight times for all flights.
    -   *Create custom delay functions*: apply a midnight crossover fix if flights are delayed over to the next day in order to calculate the delay in minutes and classify the delay status in early (current departure time is \>5 min earlier than original departure time), on time (\<5 min earlier or \<10 min later) or late (\>10 min later).
    -   *Group the different companies and flight numbers that carry the same flight*: group by variables that indicate the same flight (boarding gate, departure time, destination) is being carried by multiple companies

4.  Afterwards, it will **create a particular file** for the Airport:

    -   *Check for existing data*: checks for data in the tibbles. If there is no data, it creates no CSV.
    -   *Check for existing folder*: if a folder called `flights_data` does not exist, it creates it.
    -   *Check for existing file*: checks for files with the same name. If it doesn't exist, it creates it, and if it does, it appends the information with a time stamp.

As established in 2. Instructions: (iii) How to run this scraper?, the scraper has functions ready to automatize the whole process that we just described of one Airport for multiple Airports. If its commanded to do so, it fulfill the last task of **creating one agregated dataset that contains all the information of the Airports with upcoming flights**. It adds the source Airport for each flight and creates a new CSV if it doesn't exist yet, and if does, it updates it. The final dataset is called: `all_flights_combined.csv`.

## 4. Known limitations

This scraper requires dynamic setting of times and dates through RSelenium that detect information on the page and the actual time to run data. There are two main timing issues:

-   *Running the scraper between midnight and 1:59am.* The system time will read date `today`. However, AENA search defaults to -2hrs before your page opens. So the AENA start date that we need to use will be set as date: `today - 1`. As the scraper is set to add 6 hours from the AENA date. It ends up adding 6 hours + 1 day to the search. So you will return 6 hours of extra flight information.

-   *Running the scraper after midday AND the last day of the month.* If you search in the afternoon, the scraper will update to set the end search time to early hours of the following morning. However, if this happens on the last day of the month, the scraper cannot update the calendar effectively to change the month and then search and select the first day of the following month.

## 5. Usages

This scraper produces a clean live dataset of the current status of any Spanish Airport. Some suggested applications and research questions that may lead you to use this scraper:

1.  Check the current Airport status For example; *what's happening at Valencia Airport right now, are the flights on time? How many flights are there?*

    <div>

    To access some pre-prepared functions to apply to the output datasets, you can go to the `Sample functions to summarise and plot` .Rmd file. To run, update the Airport_data \<- read_csv() function to the location of your dataset, then run the program.

    </div>

2.  Build your own database to understand trends between and at Airports. For example:

-   Create an automatic timer to scrape all the Airport information at your time of interest and model the true Airport delays over time at your Airport of choice! Use cron or another similar program to schedule.

-   This could also be used to build a dataset to the frequency and duration of delays between different times of the day e.g. *is my flight out of Barcelona more likely to be delayed if I book in the morning or afternoon?*

3.  Update this scraping code to make your own search parameters. For example, if you want the search to be 12 hours, update the search terms within the time and calendar function to change your search times i.e. from `end_hr = start_hr +6` to `end_hr = start_hr +12`.
