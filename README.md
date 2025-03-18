# Spanish airports information scraper and delays tracker
With this scraping project you will be able to scrape AENA, a Spanish website which harvours all Spanish airports and their flights' information. 
It uses a combination of Selenium scraping to set the correct pages and times to scrape, plus traditional web scraping techniques to extract the information. 

> Brád McKenzie & Mafalda González González

## 1. Description of the project 

### Files


## 2. Instructions

- **1: setting up the Docker container**

There is two options, you can try to install Selenium, and pray that it works with your computer, or you can install Docker Desktop. Docker Desktop will enable you to sideline the Selenium program and simplify the setup process by eliminating the need to manually install browsers, WebDriver binaries, and Selenium server dependencies. Hence, it is the option we went for and recommend.
If you do not have Docker Desktop, install the correct version for your system.
Once you have Docker, start it and run the Docker command for the Selenium Firefox container in PowerShell (Windows) or Terminal (Apple). The code is below. 

```         
docker run -d -p 4445:4444 -p 5900:5900 --env VNC_NO_PASSWORD=1 --name selenium_firefox selenium/standalone-firefox-debug
```

- This will open a standalone contained that will enable us to access webpages through Selenium in R. 
- Optional: visible Firefox

    - If you would like to see how Selenium works in action, you can install a VNC Viewer, such as TigerVNC to see the live browser. 
    - Once installed, connect the port to: localhost:5900. No password required

If you want to make sure that the container is running, you can inster "docker ps" into your PowerShell / Terminal. You should be able to see the container selenium_firefox in the list. 

**2. Library requierements** 

Make sure you have the following libraries loaded: 
```
```

**3. Script**

Download `scrapie.R` from this repository and open RStudio. 
Now, you can either .... 

## 3. Explanation of the script 

Inside the script there will be a number of things happening: 

1. Rstudio will connect to the Selenium Firefox container
2. Within that container, we will load the [AENA website](https://www.aena.es/en/flight-info.html) and prepare it to be scraped. This means a number of things: 
    
    * Select airports??
    * Limit the search to only the next 6 hours of flights... BRAD
    * Read how many flights will be scraped in order to set how many times Selenium should scroll down the page. 
    * ...
    * Save that exactly that website as an HTML. 
    
3. Having established a statis HTML, the script will use with various functions to prepare a dataset of the flights of the specific Airport with general information such as their destination, the carriers or the gates, and with extra information on the delay status and time. 
4. Finally, it will create a file, `xxx`, that will have it all saved. 

## 4. Shiny App


