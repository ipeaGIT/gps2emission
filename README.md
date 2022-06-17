# gtfs2emis: Generating estimates of public transport emissions from GTFS data <img align="right" src="man/figures/logo.png" alt="logo" width="180">

[![R-CMD-check](https://github.com/rafapereirabr/gtfs2emis/workflows/R-CMD-check/badge.svg)](https://github.com/rafapereirabr/gtfs2emis/actions)
[![Lifecycle:
     experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html)
[![Codecov test
coverage](https://codecov.io/gh/rafapereirabr/gtfs2emis/branch/main/graph/badge.svg)](https://app.codecov.io/gh/rafapereirabr/gtfs2emis?branch=main)


**gtfs2emis** is an R package to estimate the emission levels of public
transport networks based on GTFS data. The package requires two main inputs: i) public transport data in the GTFS standard format; and ii) some basic information on fleet characteristics such as fleet age, technology, fuel and Euro stage. As it
stands, the the package estimates several pollutants (see table below) at high
spatial and temporal resolutions. Pollution levels can be calculated for
specific transport routes, trips, time of the day or for the transport
system as a whole. The output with emission estimates can be extracted in different
formats, supporting analysis on how emission levels vary across space,
time and by fleet characteristics.

<!-- The **gtfs2emis** package leverages on standard GTFS data format and develops a computational method that can be easily used to estimate enviromental emissions of several public transport systems around the world. One of the core advantages of **gtfs2emis** is that it makes extremely easy to simulate how the environmental performance of a public transport system would change under different policy scenarios. Simple modifications to the package input would allow one to estimate how emissions levels could be affected by different interventions such as electrifying the fleet, building new transport corridors, changing route itineraries or frequencies and fleet renewal. -->

### Installation

``` r
# From CRAN
# install.packages("gtfs2emis")
# library(gtfs2emis)
# devtools::install_github("rafapereirabr/gtfs2emis",auth_token = "94c88a642a2f62fce15e4c05cc0b6ead55ff5851")
# or use the development version with latest features
# utils::remove.packages('gtfs2emis')
# devtools::install_github("ipeaGIT/gtfs2emis")
# library(gtfs2emis)
```

### Basic Usage

The package requires two fundamental datasets.

1.  GTFS

-   Public transport data in the format of General Transit Feed
    Specification (GTFS)

2.  Fleet

-   Data frame of vehicles according to characteristics of age,
    technology, fuel, euro stage, category
-   Additional columns of urban bus route for specific allocation of
    fleet by route

## Demonstration on sample data

See detailed demonstration of `gtfs2emis` in the [intro
Vignette](https://github.com/rafapereirabr/gtfs2emis/blob/master/doc/gtfs2emis-vignette.md)
To illustrate the functionality, the package includes a small sample
data set of the public transport and fleet of Porto Alegre (Brazil).
Three steps are required to use `gtfs2emis`, as follows.

``` r
# load package
#   library(gtfs2emis)
# 
# # 1) Transport model
# 
# # Read GTFS
#   fort <- gtfs2gps::read_gtfs(system.file("extdata/fortaleza.zip"
#                                           , package = "gtfs2gps"))  %>%
#     gtfs2gps::filter_single_trip() %>% 
#     gtfs2gps::filter_by_shape_id(c("shape804-I", "shape806-I"))
#   
#  # GPS output
#   fort_gps <- gtfs2gps::transport_model()
#   
#   # 2) Fleet 
#   total_fleet <- data.table::data.table(year = c(2005,2010,2011,2012
#                                                  ,2014,2015,2017,2018,2019),
#                                         bus = c(1,61,50,1,45,18,62,27,31),
#                                         veh_type_euro = "Ubus Std 15 - 18 t",
#                                         euro_stage = c("II", "IV", "IV", "V"
#                                                        , "V", "V", "V", "V","V"))
#   
#   total_fleet[,fleet_composition := bus/sum(bus)]
#   
#   
#   # 3) Emission model
#   gtfs2emis::emission_model()
```

### Vehicle Emission Factors

Currently the package includes running exhaust emissions factors from
Environmental Company of Sao Paulo (CETESB) — Brazil; California
EMission Factor model (EMFAC2017) from California Air Resources Board —
United States; MOtor Vehicle Emission Simulator (MOVES) from
Environmental Protection Agency (EPA) — United States; European
Environment Agency (EEA) — Europe.

| Source        | Buses                        | Categories Variables                      |
|---------------|------------------------------|-------------------------------------------|
| CETESB        | Micro, Standard, Articulated | Age, Fuel, EURO stage                     |
| EMFAC model   | Urban Buses                  | Age, Fuel                                 |
| EMEP/EEA      | Micro, Standard, Articulated | Fuel, EURO stage, technology, load, slope |
| MOVES U.S EPA | Urban Buses                  | Age, Fuel                                 |

The list of pollutants is summarized below.

| Source        | Pollutants                                                                                                                        |
|---------------|-----------------------------------------------------------------------------------------------------------------------------------|
| CETESB        | CH4, CO2, PM10, N2O, KML, FC (Fuel Consumption), gD/KWH, gCO2/KWH, CO, HC, NMHC, NOx, NO2, NO, RCHO, ETOH, FS(Fuel Sales) and NH3 |
| EMFAC model   | CH4, CO, CO2, N2O, NOx, PM10, PM2.5, SOX, TOG (Total Organic Gases), ROG (Reactive Organic Gases)                                 |
| EMEP/EEA      | FC, CO2, CO, NOx, VOC, PM10, EC, CH4, NH3, N2O, and FC                                                                            |
| MOVES U.S EPA | CH4, CO, CO2, EC, HONO, N2O, NH3, NH4, NO, NO2, NO3, NOx, PM10, PM25, SO2, THC, TOG and VOC                                       |

### **Related packages**

Several others transport emissions packages are available for different
purposes:

-   [vein](https://github.com/atmoschem/vein) Bottom-up and top-down
    inventory.
-   [EmissV](https://github.com/atmoschem/emissv) Top-down inventory.
-   [Yeti](https://github.com/twollnik/YETI) (It seems abandon).
-   [PythonEmissData](https://github.com/adelgadop/PythonEmissData)
    Jupyter notebook to estimate simple top-down emissions.

### **Future enhancements**

-   Include cold-start, and evaporative emissions factors
-   Add railway emission factors

------------------------------------------------------------------------

### Credits <img align="right" src="man/figures/ipea_logo.png" alt="ipea" width="300">

The **gtfs2emis** package is developed by a team at the Institute for
Applied Economic Research (Ipea) with collaboration from the National
Institute for Space Research (INPE), both from Brazil. You can cite this
package as:

-   Bazzo, J.P.; Pereira, R.H.M.; Andrade, P.R.; (2020) gtfs2emis:
    Generating estimates of public transport emissions from GTFS data
