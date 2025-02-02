# Sattagutils {#sattagutils}

I've put together some of the functions I use most often into an R package. It isn't on CRAN but you can get it from [github](https://github.com/williamcioffi/sattagutils). Perhaps the most useful functionality of `sattagutils` is that it'll load a single tag or a directory of tags into R automatically dealing with all of the irregularities and inconsistencies in file and date formats. I've also included a handful of plotting and other tools that I tend to use a lot when I'm browsing tags.

Most of the functions are especially geared toward either SPOT or SPLASH10 tags, but could conceivable be extended for other purposes.

Skip to the [Quick guide](#sattagutils-quickguide) if you don't care about the guts of the package.

## Central concepts

### S4

Tag data streams are essentially tables with some metadata attached to them. I thought of tags as lists of data.frames with some additional metadata attached to them.

In the end I settled on S4 classes to represent tag and data objects. I know some people hate them, but in the end it seemed like the most sensible thing to do. I couldn't get the seamless functionality that I wanted out of S3. Similarly, the other OO systems in R would've gotten in the way, I think. For most operations I wanted tables to behave just like tables.

### es4dataframe

All of the various sattag data streams inherit `es4dataframe`:


```r
setClass("es4dataframe",
  contains = "data.frame"
)
```

and a constructor:


```r
es4dataframe <- function(..., stringsAsFactors = FALSE) {
  data <- data.frame(..., stringsAsFactors = stringsAsFactors)
  new("es4dataframe", data)
}
```

I implemented only the most basic functionality that I needed including `as.data.frame`, `is.es4dataframe`, `$`, `[`, `[<-`, and `merge` although I kind of regret that last one.

Most are fairly simple and just pass the buck to the underlying `data.frame`. For example:


```r
setMethod("[<-", "es4dataframe", function(x, i, j, ..., value) {
  dfin <- as.data.frame(x)
  dfout <- getS3method("[<-", "data.frame")(dfin, i, j, ..., value)

  x@.Data <- dfout
  x@names <- names(dfout)
  x@row.names <- rownames(dfout)

  return(x)
})
```

### sattagstream

A `satagstream` a  `es4dataframe` with a couple of extra slots for the type of stream and the original filename that was loaded for the stream. There are various subclass options for sattagstreams and mostly you don't need to worry about it, the primary uses are importing correctly from the [badly formated](#datastreams) Wildlife Computer csvs and guessing date formats correctly.

### sattag and tagstack

A `sattag` is a `list` with slots for instrument, DeployID, Ptt, species, location, start, end times, directory, and load date.

And finally a `tagstack` is a list of `sattag` objects with an extra slot for the directory the stack was loaded from. This is a little overkill probably it could have been just a regular `list`, but I was committed to keeping the metadata with the data at this point.

`sattag` has its own constructor and a variety of get and set methods for the slots. `[` also had to be defined explicitly to get things working as you'd expect (that is `[` should return a `sattag` and `[[` should return a `sattagstream`), but other than that lists behave a little better than `data.frames` do. `tagstack` also has a constructor and `[` as well as some get and set methods.

Everything has nice show methods so you don't blow up your console peeking at tags.

## Quick guide {#sattagutils-quickguide}

Install:


```r
devtools::install_github("williamcioffi/sattagutils")
```



Now we can load a couple of tags into a tagstack and take a peek.




```r
extags <- sattagutils::batch_load_tags("examples/tags/")
extags
```

```
## tagstack of 2 tags
## -----
## 102465 - ExampleTag001 - 14 streams
## 77246 - ExampleTag002 - 14 streams
```
So we've loaded 2 tags and we can see they both have 14 streams that were imported. We can look inside as if this was any other kind of list. Though, for many analyses we'd probably just want to start pulling out streams of interest from all the tags at this point.


```r
tag1 <- extags[[1]]
tag1
```

```
## sattag type: Mk10-A
## species: Cuvier's
## deploy id: ExampleTag001
## ptt: 102465
## start data date: 2014-05-13
## end data date: 2014-07-12
## ------
## streams: 
## 01- 102465-All.csv
## 02- 102465-Argos.csv
## 03- 102465-Behavior.csv
## 04- 102465-Corrupt.csv
## 05- 102465-Histos.csv
## 06- 102465-Labels.csv
## 07- 102465-Locations.csv
## 08- 102465-MinMaxDepth.csv
## 09- 102465-RawArgos.csv
## 10- 102465-RTC.csv
## 11- 102465-Series.csv
## 12- 102465-SeriesRange.csv
## 13- 102465-Status.csv
## 14- 102465-Summary.csv
## ------
## loaded from: examples/tags//102465
## loaded on: 2019-11-06 09:25:35
```
Here you can see a listing of all the streams in a particular tag, and we can take a closer look at the behavior table.


```r
beh <- tag1[[3]]
beh[, c('What', 'Start', 'End', 'DepthMax')]
```


```
##      What      Start        End DepthMax
## 1 Message 1399992480 1400001640       NA
## 2    Dive 1399992480 1399993560      387
## 3 Surface 1399993560 1399993650       NA
## 4    Dive 1399993650 1399994288      347
## 5 Surface 1399994288 1399994460       NA
## 6    Dive 1399994460 1399998942     1087
## ------
## stream type: behavior
## filename: 102465-Behavior.csv
```

All of the times have been converted to datenums for calculations, but can be converted back for human readability with `sattagutils::num2date`.

