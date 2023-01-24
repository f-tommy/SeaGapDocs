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
* [Tutorials: Kinematic array positioning](/tutorialkinematic/)
* [Tutorials: Static array positioning](/tutorialstatic/)
* [Tutorials: MCMC static array positioning](/tutorialmcmcpvg/)
* [Tutorials: Line fitting for time-series](/tutorialts/)
* [Others](/others/)

