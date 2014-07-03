---
title       : The EML R package
subtitle    : Metadata integration into R
author      : Claas-Thido Pfaff
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : [mathjax, bootstrap]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
github: 
  author: ctpfaff
  repo: emlrpackage
---



## About the package
<hr />
<br>
<br>

`EML for R` allows 

* reading
* describe
* publishing 

data using the Ecological Metadata Language (EML) format.

---

## About the package
<hr />

* Part of the rOpenSci (Data-Acess, Vizualisation, Reproducibility... 30+)
* Developed by:

<img src="assets/img/ropensci_all_developers.png" style="width: 700px;"/>

* Advised: Matt Jones (EML maintainer), Duncan T. Lang (R-Core development)

---

## About the package
<hr />
<br>

* Motivation:

- Many ecol. data lacks description
  + is hard to find/understand/reuse
  
- Good metadata:
  
  + Unerstand
  + Better reuse (reduce redundancy)
  + Potentially more visibility

---

## Install the package
<hr />
<br>

* Source code:
  - https://github.com/ropensci/EML

* From Github with (as in development): 

```
library("devtools")
install_github("EML", "ropensci")
library("EML")
```

* If vignettes building fails

```
install_github("EML", "ropensci", build_vignettes=FALSE)
```

---

## Read metadata
<hr />

* example the BEF-China database (column definition, coverage, contact)
* URL EML file: (http://china.befdata.biow.uni-leipzig.de/datasets/334.eml)

<img src="assets/img/befdata_example.png" style="width: 600px;"/>

---

## Read metadata
<hr />

* Get the metadata (File, URL, KNB-ID)


```r
metadata <- eml_read("http://china.befdata.biow.uni-leipzig.de/datasets/334.eml")
```

* Then use `eml_get(metadata, "xy")`
  - csv_filepaths
  - coverage
  - contact
  - unit.defs
  - col.defs
  - creator
  - data.set ...

---

## Read metadata
<hr />


```r
eml_get(metadata, "col.defs")[1]
```

```
##                                                                                                                                                                                                                                                                                                                              attribute 
## "Plot number according to design spreadsheet. Plots are named according to their location on the original spreadsheet used for treatment design. This has letters from West to East and numbers from North to south. Some plots were moved half a gridcell (1 mu) so be suitable for planting (grid cell loctype). (plot_id: plot_id)"
```


```r
eml_get(metadata, "creator")
```

```
## [1] "Anne Lang <anne_christina.lang@uni-leipzig.de> [cre]"
## [2] "Werner HÃ¤rdtle <haerdtle@uni-lueneburg.de> [cre]"   
## [3] "Goddert von Oheimb <vonoheimb@uni.leuphana.de> [cre]"
```

---

## Read metadata
<hr />


```r
eml_get(metadata, "coverage")
```

```
## geographicCoverage:
##   geographicDescription: "The experiment was set up in the northeast part of Jiangxi
##     Province, P.R. China (N 29Â° 06.29 E 117Â° 55.28). \n"
##   boundingCoordinates:
##     westBoundingCoordinate: '117.89978'
##     eastBoundingCoordinate: '118.148346'
##     northBoundingCoordinate: '29.285201'
##     southBoundingCoordinate: '29.101777'
## temporalCoverage:
##   rangeOfDates:
##     beginDate:
##       calendarDate: |2
## 
##         2009-08-26
##     endDate:
##       calendarDate: |2
## 
##         2010-09-26
## taxonomicCoverage:
##   taxonomicClassification:
##     commonName: |2+
## 
##       tree species (Schima superba, Elaeoarpus decipiens, Castanea henryi, Quercus serrata)
```

---

## Read metadata
<hr />
<br>

* Extract the data (open access):



```
example_dataset = eml_get(metadata, "data.set")
```


```r
example_dataset[1:4, 1:3]
```

```
##     plot_id             Spp Leaf_15N_recovery_g_T5
## 1 pilot1C09 Castanea henryi              1.504e-05
## 2 pilot1D10 Castanea henryi              8.604e-05
## 3 pilot2C11 Castanea henryi              2.905e-05
## 4 pilot2D01 Castanea henryi              8.923e-06
```

--- 

## Describe your own
<hr />


```r
river = c("SAC",  "SAC",   "AM")
spp   = c("king",  "king", "ccho")
stg   = c("smolt", "parr", "smolt")
ct    = c(293,    410,    210)
```


```r
undescribed_dataframe = data.frame(river, spp, stg, ct)
undescribed_dataframe
```

```
##   river  spp   stg  ct
## 1   SAC king smolt 293
## 2   SAC king  parr 410
## 3    AM ccho smolt 210
```

* EML extends `data.frame` with `data.set` class (additional params)

---
  
## Describe your own
<hr />

* `col.defs` (plain text definition)


```r
col_defs = c("River site used for collection",
             "Species common name",
             "Life Stage",
             "count of live fish in traps")
```
  
  - `unit.defs` (factor => levels, dates => YYYY or MM-DD-YY, numeric => unit list KNB)


```r
unit_defs = list(c(SAC = "The Sacramento River", AM = "The American River"),
                 c(king = "King Salmon", ccho = "Coho Salmon"),
                 c(parr = "third life stage", smolt = "fourth life stage"),
                 "number")
```

---

## Describe your own
<hr />

* Use the prepared variables:


```r
described_dataset = data.set(river = c("SAC",  "SAC", "AM"),
                             spp   = c("king",  "king", "ccho"),
                             stg   = c("smolt", "parr", "smolt"),
                             ct    = c(293,    410,    210),
                             col.defs = col_defs,
                             unit.defs = unit_defs)
```

* Convert: 

```
data.set(undescribed_dataframe, col.def = col_defs, uni.defs = unit_defs)
```

---

## Describe your own. More!
<hr />

```
- eml
  - dataset
    - creator (add)
    - contact (add)
    - publisher
    - title (add)
    - pubDate
    - keywords
    - abstract 
    - intellectualRights (resuse)
    - methods
    - coverage
    - dataTable
      - physical
      - attributeList
  - additionalMetadata
```

---


## Describe your own. More!
<hr />

* Create person


```r
claas = eml_person("Claas-Thido Pfaff <fake@test.com>")                            
```

* Coerce to creator/contact class (subsettable via @)


```r
creator_claas = as(claas, "creator")
contact_claas = as(claas, "contact")
creator_claas@individualName
```

```
## An object of class "individualName"
## Slot "salutation":
## character(0)
## 
## Slot "givenName":
## [1] "Claas-Thido"
## 
## Slot "surName":
## [1] "Pfaff"
```

---

## Describe your own. More!
<hr />

* Create address


```r
address <- new("address",
               deliveryPoint = "Universität Leipzig, Johannisallee 21",
               city = "Leipzig",
               postalCode = "04103",
               country = "GER") 
```

* Add address (creator/contact)


```r
creator_claas@address = address
contact_claas@address = address
creator_claas@address@city
```

```
## [1] "Leipzig"
```

---


## Describe your own. More!
<hr />
<br/>

* Create a title 
  - (`string` also e.g intellectual rights,abstract ... )


```r
title <- "This is my first dataset title with EML from R"
```

* Reuse intellectual rights!


```r
metadata <- eml_read("http://china.befdata.biow.uni-leipzig.de/datasets/334.eml")
```


```r
license = metadata@dataset@intellectualRights
```

---

## Describe your own. More!
<hr />
<br/>

* License 


```r
unlist(strsplit(metadata@dataset@intellectualRights, split="\\."))[1]
```

```
## [1] "Permission is granted to anybody to access, use and publish all open for public data freely"
```

* Assemble everything
  - Dataset `dataset_described`
  - title `title`
  - creator `creator_claas`
  - contact `contact_claas`
  - usage rights `license`

---

## Describe your own. More!
<hr />



* Create the eml object: 


```r
data <- eml(dat = described_dataset,
            title = title,
            creator = creator_claas,
            contact = contact_claas,
            intellectualRights = license,
          )
```


```r
slotNames(new("dataset"))[1:10]
```

```
##  [1] "alternateIdentifier" "shortName"           "title"              
##  [4] "creator"             "metadataProvider"    "associatedParty"    
##  [7] "pubDate"             "language"            "series"             
## [10] "abstract"
```

---

## Describe your own. Write!

* Write it to a file.


```r
eml_write(data, file="mymetadata.xml")
```

```
## [1] "mymetadata.xml"
```

* Validate it!


```r
eml_validate("mymetadata.xml") 
```

```
## EML specific tests XML specific tests 
##               TRUE               TRUE
```

---

## Publish it!
<hr />
<br>

* Currently supported (figshare, knb)
* Example figshare:


```r
eml_publish("mymetadata.xml", 
            description="My example EML file",
            categories = "Ecology", 
            tags = "EML", 
            destination="figshare") 
```

* Note requires the `rfigshare` package + account
* Creates a draft file (visible only to you)

---

## What's next?
<hr />
<br>
<br>

* We work on a better cover of EML modules
* Write smart helper functions 
  - put away the s4 class from (eml, eml_user, eml_get)
* Better documentation
* Hopefully soon a stable CRAN release

---

## Potential? Wrap-Up
<hr />
<br>
<br>

* The `EML` package: 

  - Simplify metadata creation (reuse)
  - Helps understanding data
  - Fast import of data (R)
  - Raise visibility (publilshing)

---

<br>
<br>

<div class = "flushcenter">
     <h1>Thanks for your attention!</h1>
     <h1>Any questions?</h1>
</div>

<br>
<br>

https://ctpfaff.github.com/emlrpackage

