+++
title = "Methodology"
hascode = true
rss = "A short description of the page which would serve as **blurb** in a `RSS` feed; you can use basic markdown here but the whole description string must be a single line (not a multiline string). Like this one for instance. Keep in mind that styling is minimal in RSS so for instance don't expect maths or fancy styling to work; images should be ok though: ![](https://upload.wikimedia.org/wikipedia/en/b/b0/Rick_and_Morty_characters.jpg)"
rss_title = "Overview"
rss_pubdate = Date(2019, 5, 1)

tags = ["syntax", "code", "image"]
+++

# Overview

GNSS-A positioning was contrived by [Spiess (1985)](https://ieeexplore.ieee.org/document/4072330), and the geodetical results were firstly given by [Spiess et al. (1998)](https://www.sciencedirect.com/science/article/abs/pii/S0031920198000892).
Then, it has been developed by various researchers, especially in Japan (Japan Coast Guard, Nagoya Univ., and Tohoku Univ.).

Each GNSS-A site is composed of multiple seafloor transponders (minimum: 3), which is called a seafloor transponder array.
In the GNSS-A positioning, we first determine initial positions of the seafloor transponders (corresponding to "pxp-ini.inp" in SeaGap).
Then, we estimate an array displacement of the seafloor transponders from the initial positions assuming the seafloor transponder array rigidly moves among the repeated GNSS-A campaigns.
This estimation is performed by estimating a common position change among the seafloor transponders for each campaign.

The most accurate technique to determin the initial positions of the seafloor transponders is the estimation using various campaign data (e.g., [Honsho & Kido, 2017](https://agupubs.onlinelibrary.wiley.com/doi/full/10.1002/2017JB014733)).
The SeaGap do not have such an elaborated function, but has a function to determine positions of the individual seafloor transponders using single campaign data [Individual transponder positioning `static_individual()`].
It is a simple but powerful way that we first estimate positions of the individual seafloor transponders from single campaign data and then average them from multiple campaigns as the initial positions.

## Contents

* [Methodology: Travel-time calculation](/methodtt/)
* [Methodology: Positioning methods](/methodstatic/)  


