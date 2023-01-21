+++
title = "Dataformat"
hascode = true
date = Date(2019, 3, 22)
rss = "A short description of the page which would serve as **blurb** in a `RSS` feed; you can use basic markdown here but the whole description string must be a single line (not a multiline string). Like this one for instance. Keep in mind that styling is minimal in RSS so for instance don't expect maths or fancy styling to work; images should be ok though: ![](https://upload.wikimedia.org/wikipedia/en/3/32/Rick_and_Morty_opening_credits.jpeg)"

tags = ["syntax", "code"]
+++


# Overview

Documentation for the input dataformat in [SeaGap](https://github.com/f-tommy/SeaGap.jl).

In SeaGap, the following input data files are basically required:

\toc

The above file names are given as default values, and they can be optionally changed.
These files are text data format with delimitation of space.


## tr-ant.inp

Three components of position of a transducer attached to a sea-surface platform relative to the GNSS antenna position are shown in this file.
Using these values, the GNSS antenna positions (written in [obsdata.inp](#obsdata.inp)) are converted into the transducer positions with the right-handed system. Note that this definision is different from [GARPOS](https://github.com/s-watanabe-jhod/garpos).

The file is an one-lined file with three columns, and each column is given as (1: X, 2: Y, 3: Z).
For example:

**$ cat tr-ant.inp**
```
0.188984299 -6.990937724 -15.300000000
```

## pxp-ini.xyh

Positions of individual seafloor transponders at each site are given in the file. The positions must be given in meter. In order to provide these positions, you have to convert geographic coordinates into the projection coordinates. The projection center should be defined as a center of the transponder array for each site, and must be fixed among GNSS-A campaign cruises. You can convert the geographic coordinates into the projection coordinates by ll2xy function shown in Others via [GMT](https://github.com/GenericMappingTools/GMT.jl)'s mapprojection module

The file has three rows, which indicate the transponder positions in meter (1: EW position, 2: NS position, 3: UD position).
Each line shows one seafloor transponder position; thus, the total number of lines indicate the total number of transponders at each site.
The line number of this file corresponds to the transponder number written in [obsdata.inp](#obsdata.inp).

**$ cat pxp-ini.xyh**
```
 997.5346  985.5925 -2738.9998
-956.8931  941.9216 -2683.2858
-977.3029 -968.6586 -2689.2996
 935.8767 -980.5919 -2710.8535
``` 

## ss\_prof.zv

Underwater sound speed profile is given in this file. In order to precise calculation of travel-times, you should prepare a profile with 5-m or less interval. The rows of this file indicate (1: water depth [m], 2: sound speed [m/sec]), respectively.

$ head ss\_prof.zv
```
 0.00 1526.998
 5.00 1529.598
10.00 1529.594
15.00 1529.637
20.00 1529.684
25.00 1529.748
30.00 1529.343
35.00 1528.586
40.00 1527.722
45.00 1525.194
```  

Maximum depth of ss\_prof.zv must be deeper than maximum depth of the seafloor transponders wriiten in "pxp-ini.xyh". Thus, you should prepare a long profile deeper than the water depth by extrapolation.

You can optionally draw figure of the sound speed profile by `plot_prof(;fno,fn)` function. `fn` is the file name of a sound speed profile (the default is `fn="ss_prof.zv"`). `fno` is the output file name (the default is `fno="ss_prof.pdf"`). If you change the filename extension, the output file format is automatically changed (e.g., ".png",".svg"). If you use this function with `show=true` keyword argument in REPL, you can display as GUI (but not saved as a figure file). 

```julia
SeaGap.plot_prof(fno="ss_prof.png",fn="ss_prof.zv",show=false) 
```

~~~
<div class="row">
  <div class="container">
    <img class="left" src="/assets/ss_prof.png">
    <div style="clear: both"></div>      
  </div>
</div>
~~~
