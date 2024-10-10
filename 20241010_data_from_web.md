Data from Web
================
Kaleb J. Frierson
2024-10-10

# Notes

There are two components when looking at a website:

HTML code with all the information for structure and content (ML =
markup language).

CSS styling side produces what you see.

There are challenges with extracting what you want from an HTML file.
You have to pick out the pieces you want. A big piece of getting data
from the web is using a CSS selector: how do you look at a website and
figure out from that website what is the correct css tag to give you the
actual data that you want and none of the data you don’t want.

- Selector Gadget is the most common tool for finidng the right CSS
  selector on a page. In a browser, you go to the page you care about,
  launch SG, click on the things you want. rvest package helps scrape
  data from the web.

Workflow:

download HTML using read_html () ()

OR

In contrast to scraping, you can use an application programming
interface (API) which provides a way to communicate with software. Web
APIs may give you a way to request specific data from a server. Some API
have a download option for CSV. Sometimes this isn’t ideal when data
changes all the time and you could have code that ALWAYS grabs the most
current data and then can run an UTD report.

Data from the web is messy, it will take a lot of work to figure it out
and get it tidy.

# Coding

## Library Calling

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)
```

## Data From Web

define the url then create an HTML that reads that HTML:

``` r
url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)

drug_use_html
```

    ## {html_document}
    ## <html lang="en">
    ## [1] <head>\n<link rel="P3Pv1" href="http://www.samhsa.gov/w3c/p3p.xml">\n<tit ...
    ## [2] <body>\r\n\r\n<noscript>\r\n<p>Your browser's Javascript is off. Hyperlin ...

To get the pieces we actually need:

``` r
marj_use_df = 
drug_use_html |> 
  html_table() |> 
  first() |> 
  slice(-1) # takes first element out of the table 
```

## Learning Assessment

``` r
nyc_cost = 
  read_html("https://www.bestplaces.net/cost_of_living/city/new_york/new_york") |>
  html_table(header = TRUE) |>
  first()

nyc_cost
```

    ## # A tibble: 8 × 4
    ##   Categories       `New York` `New York` `United States`
    ##   <chr>            <chr>      <chr>      <chr>          
    ## 1 Overall          172.5      121.5      100.0          
    ## 2 Grocery          116.6      103.8      100            
    ## 3 Health           127.6      120.7      100.0          
    ## 4 Housing          224.3      127.9      100.0          
    ## 5 Median Home Cost $677,200   $413,600   $338,100       
    ## 6 Utilities        150.5%     115.9%     100.0%         
    ## 7 Transportation   181.1      140.7      100.0          
    ## 8 Miscellaneous    136.4      121.8      100.0

## CSS Selectors

``` r
swm_html = 
  read_html("https://www.imdb.com/list/ls070150896/")
```

``` r
title_vec = 
  swm_html |>
  html_elements(".ipc-title-link-wrapper .ipc-title__text") |>
  html_text()

metascore_vec = 
  swm_html |>
  html_elements(".metacritic-score-box") |>
  html_text()

runtime_vec = 
  swm_html |>
  html_elements(".dli-title-metadata-item:nth-child(2)") |>
  html_text()

swm_df = 
  tibble(
    title = title_vec,
    score = metascore_vec,
    runtime = runtime_vec)

swm_df
```

    ## # A tibble: 9 × 3
    ##   title                                             score runtime
    ##   <chr>                                             <chr> <chr>  
    ## 1 1. Star Wars: Episode I - The Phantom Menace      51    2h 16m 
    ## 2 2. Star Wars: Episode II - Attack of the Clones   54    2h 22m 
    ## 3 3. Star Wars: Episode III - Revenge of the Sith   68    2h 20m 
    ## 4 4. Star Wars: Episode IV - A New Hope             90    2h 1m  
    ## 5 5. Star Wars: Episode V - The Empire Strikes Back 82    2h 4m  
    ## 6 6. Star Wars: Episode VI - Return of the Jedi     58    2h 11m 
    ## 7 7. Star Wars: Episode VII - The Force Awakens     80    2h 18m 
    ## 8 8. Star Wars: Episode VIII - The Last Jedi        84    2h 32m 
    ## 9 9. Star Wars: Episode IX - The Rise of Skywalker  53    2h 21m

## Learning Assessment

``` r
url = "http://books.toscrape.com"

books_html = read_html(url)

books_titles = 
  books_html |>
  html_elements("h3") |>
  html_text2()

books_stars = 
  books_html |>
  html_elements(".star-rating") |>
  html_attr("class")

books_price = 
  books_html |>
  html_elements(".price_color") |>
  html_text()

books = tibble(
  title = books_titles,
  stars = books_stars,
  price = books_price
)
```
