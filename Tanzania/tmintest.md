Habiba
================
Rasmus Benestad
April 29, 2016

R Markdown
----------

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

Load the esd-package and the data:

``` r
library(esd)
load('tmininTz.rda')
load('predictors.rda')
data(ferder)
```

This is what we expect to see with good correspondence between predictand and predictor

``` r
djf.e <- subset(as.4seasons(ferder),it='Jan')
corfield(djf.e,DJF.E,colbar=list(breaks=seq(-1,1,by=0.1),rev=FALSE),new=FALSE)
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-2-1.png)<!-- -->

Now compare the station data with the corresponding interpolated data from the reanalysis

``` r
dje.ei <- regrid(DJF.E,is=djf.e)
plot(djf.e,new=FALSE)
lines(dje.ei,lwd=3)
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-3-1.png)<!-- -->

**Tanzania**
------------

Now we have a look at some of the stations in Tanzania: the ones with the poorest and the best score for the season with the lowest score. The first one is for Mwanza December-February mean daily minimum temperature:

``` r
y <- subset(Y,is='Mwanza')
djf <- subset(as.4seasons(y),it='Jan')
## fix a little bug
attr(djf,'unit') <- 'degC'
corfield(djf,DJF,colbar=list(breaks=seq(-1,1,by=0.1),rev=FALSE),new=FALSE)
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-4-1.png)<!-- -->

We can interpolate temperatures for the same coordinate as for Mwanza, but when we do the comparison, we need to use standardised values as the predictor represents the mean of daily mean temperatures \(\overline{T}\) whereas the station is the mean daily minimum \(T[min]\):

``` r
djf.i <- regrid(DJF,is=y)
plot(stand(djf),new=FALSE)
lines(stand(djf.i),lwd=3)     
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-5-1.png)<!-- -->

Dodoma was the station with the best correspondence between predictor and predictand:

``` r
y2 <- subset(Y,is='Dodoma')
## There were two entries for Dodoma - exclude the one from GHCN
y2 <- subset(y2,is=1)
djf2 <- subset(as.4seasons(y2),it='Jan')
## fix a little bug
attr(djf2,'unit') <- 'degC'
corfield(djf2,DJF,colbar=list(breaks=seq(-1,1,by=0.1),rev=FALSE),new=FALSE)
```

``` r
djf.i2 <- regrid(DJF,is=y2)
plot(stand(djf2),new=FALSE)
lines(stand(djf.i2),lwd=3)     
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-7-1.png)<!-- -->

Songea was the station with the best scores in the March-May season

``` r
y3 <- subset(Y,is='Songea')
y3 <- subset(y3,is=1)
mam <- subset(as.4seasons(y3),it='Apr')
## fix a little bug
attr(mam,'unit') <- 'degC'
corfield(mam,MAM,colbar=list(breaks=seq(-1,1,by=0.1),rev=FALSE),new=FALSE)
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-8-1.png)<!-- -->

``` r
mam.i <- regrid(MAM,is=y3)
plot(stand(mam),new=FALSE)
lines(stand(mam.i),lwd=3)     
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-9-1.png)<!-- -->

Have a look at the group of stations
------------------------------------

The use of principal component analysis (PCA) can reveal problems with some stations, since it will estimate patterns of covariance. We can apply PCA to the MAM season aggregated minimum temperature:

``` r
tmin <- subset(Y,is=1:13)
tmin <- subset(as.4seasons(tmin,nmin=60),it='Jan')
diagnose(tmin)
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-10-1.png)<!-- -->

``` r
tmin <- pcafill(tmin)
```

Here is a plot of the evolution of the MAM mean minimum temperature:

``` r
plot(tmin,new=FALSE)
```

![](tmintest_files/figure-markdown_github/unnamed-chunk-11-1.png)<!-- -->

The PCA suggests that there is one main pattern of variability, explaining 76% of the variance. The temperatures tend to increase over time, except for Mwanza (represented with the dark blue symbols which indicates negative weights). The other stations have similar weights, with most pronounced variability/trend near the coast.

``` r
plot(PCA(tmin),new=FALSE)
```

    ## Warning in plot.xy(xy.coords(x, y), type = type, ...): "plot" is not a
    ## graphical parameter

![](tmintest_files/figure-markdown_github/unnamed-chunk-12-1.png)<!-- -->
