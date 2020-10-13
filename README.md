Assignment 3
================
Edward Kim
10/13/2020

``` r
library(tidyverse)
library(httr)
library(xml2)
library(dplyr)
```

``` r
#Get Number of Searchs 
website <- xml2::read_html("https://pubmed.ncbi.nlm.nih.gov/?term=sars-cov-2+trial+vaccine")
counts <- xml2::xml_find_first(website,"/html/body/main/div[9]/div[2]/div[2]/div[1]/span")
counts <- as.character(counts)
stringr::str_extract(counts,"[0-9]+")
```

    ## [1] "560"

There were 560 results when the term “sars-cov-2 trial vaccine” was
searched on the pubmed.gov website.

``` r
#Get the ids
query <- httr::GET(
  url = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi",
  query = list(db = "pubmed",
               term = "sars-cov-2 trial vaccine",
               retmax = 1000
        )
)

query <- httr::content(query)
query_txt <- as.character(query)

ids <- stringr::str_extract_all(query_txt, "<Id>[0-9]+</Id>")[[1]]
ids <- stringr::str_remove_all(ids, "<Id>|</Id>")
```

``` r
publications <- GET(
    url = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi",
    query = list( db = "pubmed",
                  id = paste(ids,collapse = ","),
                  retmx = 1000,
                  rettype = "abstract"
        )
    )

publications <- httr::content(publications)
publications_txt <- as.character(publications)
```
