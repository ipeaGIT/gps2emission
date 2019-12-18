Introduction
============

Package `gtfs2gps` allows users to convert public transport GTFS data
into a single `data.table` format with GPS-like records, which can then
be used in various applications such as running transport simulations or
scenario analyses. Before using the package, just install it from
GitHub.

    devtools::install_github("ipeaGIT/gtfs2gps")

Loading data
============

After loading the package, GTFS data can be read into R by using
`read_gtfs()`. This function gets a zipped GTFS file and returns a list
of `data.table` objects. The returning list contains the data of each
GTFS file indexed according to their file names without extension.

`{r echo = FALSE} library("gtfs2gps") sao <- read_gtfs(system.file("extdata/saopaulo.zip", package="gtfs2gps")) names(sao) sao$trips`

Note that not all GTFS files are loaded into R. This function only loads
the necessary data to spatially and temporally handle trips and stops,
which are: - agency.txt - calendar.txt - routes.txt - shapes.txt -
stop\_times.txt - stops.txt - trips.txt - frequencies.txt (this last one
is optional).

If a given GTFS zipped file does not contain all of these required files
then `read_gtfs()` will stop with an error.

Subsetting GTFS Data
====================

GTFS data sets can be fairly large for complex public transport networks
and, in some cases, users might want to focus on specific transport
services at week days/weekends, or on specific trips or routes. The
package brings some functions to filter GTFS.zip and speed up the data
processing.

These functions subset all the relevant GTFS files in order to remove
all the unnecessary rows, keeping the data consistent. The returning
values of the four functions is a list of `data.table` objects, in the
same way of the input data. For example, in the code below we filter
only shape ids between 53000 and 53020.

    library(magrittr)
    object.size(sao) %>% format(units = "Kb")
    sao_small <- gtfs2gps::filter_by_shape_id(sao, c(51338, 51956, 51657))
    object.size(sao_small) %>% format(units = "Kb")

We can then easily convert the data to simple feature format and plot
them.

    sao_small_shapes_sf <- gtfs2gps::gtfs_shapes_as_sf(sao_small)
    sao_small_stops_sf <- gtfs2gps::gtfs_stops_as_sf(sao_small)

    plot(sf::st_geometry(sao_small_shapes_sf))
    plot(sf::st_geometry(sao_small_stops_sf), pch = 20, col = "red", add = TRUE)
    box()

After subsetting the data, it is also possible to save it as a new GTFS
file using `write_gtfs()`, as shown below.

    write_gtfs(sao_small, "sao_small.zip")

Converting to GPS-like format
=============================

To convert GTFS to GPS-like format, use `gtfs2gps()`. This is the core
function of the package. It takes a GTFS zipped file as an input and
returns a `data.table` where each row represents a ‘GPS-like’ data point
for every trip in the GTFS file. In summary, this function interpolates
the space-time position of each vehicle in each trip considering the
network distance and average speed between stops. The function samples
the timestamp of each vehicle every 15*m* by default, but the user can
set a different value in the `spatial_resolution` argument. See the
example below.

      sao_gps <- gtfs2gps("sao_small.zip", progress = FALSE, cores = 1, spatial_resolution = 15)
      head(sao_gps)

The following figure maps the first 100 data points of the sample data
we processed.

      shapes_sf <- gps_as_sf(sao_gps)
      sao_gps60 <- sao_gps[1:100, ]
      sao_gps60_sf <- gps_as_sf(sao_gps60)
      
      plot(sf::st_geometry(sao_gps60_sf), pch = 20)
      plot(sf::st_geometry(sao_small_shapes_sf), col = "blue", add = TRUE)
      box()

The function `gtfs2gps()` automatically recognises whether the GTFS data
brings detailed `stop_times.txt` information or whether it is a
`frequency.txt` GTFS file. A sample data of a GTFS with detailed
`stop_times.txt` cab be found below:

    poa <- system.file("extdata/poa.zip", package="gtfs2gps")
    poa_gps <- gtfs2gps(poa, progress = FALSE)
    poa_gps_sf <- gps_as_sf(poa_gps)
    poa_sf <- read_gtfs(poa) %>% gtfs_shapes_as_sf()
    plot(sf::st_geometry(poa_gps_sf[1:200,]))
    plot(sf::st_geometry(poa_sf), col = "blue", add = TRUE)
    box()

Methodological note
===================

For a given trip, the function `gtfs2gps` calculates the average speed
between each pair of stops — given by the ratio between cumulative
network distance *S* and departure time *t* for a consecutive pair of
valid stop\_ids (*i*),
$$
\\overline{Speed\_i} = \\frac{S\_{i+1}-S\_i}{t\_{i+1}-t\_i}.
$$
Since the beginning of each trip usually starts before the first
stop\_id, the mean speed cannot be calculated as shown in the previous
equation, because information on *i* period does not exist. In this
case, the function consider the mean speed for the whole trip. It also
happens after the last valid stop\_id (*N*) of the trips, where info on
*i* + 1 also does not exist.

$$
Speed\_N = Speed\_0 = \\overline{Speed} = \\frac{S\_N-S\_1}{t\_N-t\_1}
$$
![alt
text](https://github.com/ipeaGIT/gtfs2gps/blob/master/vignettes/speed.png)

Final remarks
=============

If you have any suggestions or want to report an error, please visit the
GitHub page of the package [here](https://github.com/ipeaGIT/gtfs2gps).
