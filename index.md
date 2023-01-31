@def title = "SeaGap Documents"
@def tags = ["syntax", "code"]

# Welcome to SeaGap!

~~~
<div class="row">
  <div class="container">
    <img src="/assets/twitter_header_photo_2.png" width="200" height="200">
    <div style="clear: both"></div>      
  </div>
</div>
~~~

SeaGap is a new **S**oftware of the **e**nhanced **a**nalysis for **G**NSS-**a**coustic **p**ositioning.

SeaGap is available on Github: [https://github.com/f-tommy/SeaGap](https://github.com/f-tommy/SeaGap)!

But, note that SeaGap is now pre-opened; thus, some programs or data-format may be suddenly changed.

## Overview

SeaGap is written in Julia for fast calculation. SeaGap is designed to perform "text-based processing" that SeaGap reads data from the input text file, performs calculation, and then writes the output text file instead of holding the calculation results as variables in Julia.

GNSS-Acoustic (GNSS-A) technique is a combination technique of GNSS positioninig on a sea-surface platform and acoustic ranging between a sea-surface transducer for seafloor geodesy, which is contrived by Dr. Spiess from the Scripps Institution of Oceanography in 1980s. The GNSS-A technique has been developed by various researchers. Altanative tool for GNSS-A positioning is [GARPOS](https://github.com/s-watanabe-jhod/garpos) develped by [Watanabe et al. (2020)](https://www.frontiersin.org/articles/10.3389/feart.2020.597532/full).

SeaGap provides various functions to easily perform GNSS-A positioning, post-processing for the positioning results, and visualization of the results. Moreover, SeaGap covers various types of positioning: classic kinematic array positioning (e.g., [Kido et al. 2006](https://earth-planets-space.springeropen.com/articles/10.1186/BF03351996)), static array positioning (e.g., [Tomita and Kido, 2022](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-022-01740-0)), MCMC-base static array positioning (e.g., [Tomita and Kido, 2022](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-022-01740-0)), static positioning for an individual transponder, and static array positioning with estimation of an offset between a sea-surface GNSS antenna and a sea-surfacetransducer.

You should refer  [Tomita and Kido, 2022](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-022-01740-0) for the citation of this software (note that an article  for this software is preparing). 

## Contents

* [Installation](/install/)
* [Dataformat](/dataformat/)
* [Methodology: Overview](/methodoverview/)
* [Methodology: Travel-time calculation](/methodtt/)
* [Methodology: Kinematic array positioning](/methodkinematic/)
* [Methodology: Static array positioning](/methodstatic/)
* [Methodology: Individual transponder positioning](/methodsingle/)
* [Methodology: Static arry positioning with gradients](/methodmcmcpvg/)
* [Tutorials: Forward calculation](/tutorialforward/)
* [Tutorials: Outlier removal](/tutorialdenoise/)
* [Tutorials: Kinematic array positioning](/tutorialkinematic/)
* [Tutorials: Static array positioning](/tutorialstatic/)
* [Tutorials: MCMC static array positioning](/tutorialmcmcpvg/)
* [Tutorials: Line fitting for time-series](/tutorialts/)
* [Others](/others/)

## Function help

If you'd like to know a detailed usage of each function, you can confirm it by "help" mode:

```julia-repl
julia> import SeaGap
julia> ?
help?> SeaGap.plot_map_array_each
  plot_map_array_each(xrange,yrange; autoscale,fn,fno,plot_size,lmargin,tmargin,bmargin,rmargin,show,ms,gfs,col_num)

  Make a figure plotting the estimated array displacements obtained by pos_array_each() in a horizontal map.

    •  xrange and yrange: EW and NS ranges for plot [m]

    •  autoscale: If autoscale=true (default), the plot range is automatically determined. If
       autoscale=false, the plot range is fixed by xrange and yrange.

    •  fn: Input file name (fn="array_each.out" in default)

    •  fno: Output figure name (fno="map_array_each.pdf" in default)

    •  plot_size: Figure size (plot_size=(600,500) in default)

    •  lmargin: Plot margin for the left edge (lmargin=2.5 in default)

    •  rmargin: Plot margin for the right edge (rmargin=1.0 in default)

    •  tmargin: Plot margin for the top edge (tmargin=1.0 in default)

    •  bmargin: Plot margin for the bottom edge (bmargin=1.0 in default)

    •  show: if show=true, a figure is temporally shown; if false, the figure is save as fno (show=false
       in default)

    •  ms: Plotted marker size (ms=5 in default)

    •  gfs: Fontsize for label (gudefontsize: gfs=12 in default)

    •  col_num: If col_num=1 (default), the plot is colored by the observation time. If col_num=2, the
       plot is colored by number of the used observational data. If col_num=0, the plot is colored by
       blue.

  Example
  ≡≡≡≡≡≡≡≡≡

  SeaGap.plot_map_array_each((-0.5,0.5),(-0.5,0.5),autoscale=false)
```
