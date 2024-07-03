+++
title = "Methodology"
hascode = true
rss = "A short description of the page which would serve as **blurb** in a `RSS` feed; you can use basic markdown here but the whole description string must be a single line (not a multiline string). Like this one for instance. Keep in mind that styling is minimal in RSS so for instance don't expect maths or fancy styling to work; images should be ok though: ![](https://upload.wikimedia.org/wikipedia/en/b/b0/Rick_and_Morty_characters.jpg)"
rss_title = "Positioning method"
rss_pubdate = Date(2019, 5, 1)

tags = ["syntax", "code", "image"]
+++

# Static array positioning

\toc

## Overview

SeaGap equips various positioning functions both for the array positioning and individual transponder positioning.
The details of the functions are written in [Tomita and Kido, 2024](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-024-01987-9).

## Individual transponder positioning

`static_individual()`:
A function to estimate individual seafloor transponder positions and to model the sound speed fluctuation using 3d B-spline functions based on the concept of NTD (Nadir Total Delay).
Then, `static_individual` estimates coefficients for the fixed number of the 3d B-spline functions as well as the individual seafloor transponder positions during each campaign through the Gauss-Newton method.

## Array positioning


`kinematic_array()`:

A function to estimate a horizontal array displacement for each shot group (kinematic array positioning) by Gauss-Newton method.
The original kinematic array positioning method ([Kido et al. (2006)](https://earth-planets-space.springeropen.com/articles/10.1186/BF03351996) and [Kido et al. (2008)](https://earth-planets-space.springeropen.com/articles/10.1186/BF03352785)) estimates an array position for each acoustic ping; `kinematic_array()` enables us to estimate an array displacement for an user's defined shot group.

`kinematic_array_3d()`:

A function to estimate a 3D array displacement for each shot group (kinematic array positioning) by Gauss-Newton method.
This method corresponds to "the conventional method estimating uplift" in [Tomita et al. (2019)](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-019-1082-y).

`static_array()`:

A function to estimate static array position and temporal sound speed fluctuation (static array positioning).
The temporal sound speed fluctuation modeled as NTD is generally modeled by a kind of flexible functions, such as 3d B-spline functions (e.g., [Honsho & Kido, 2017](https://agupubs.onlinelibrary.wiley.com/doi/full/10.1002/2017JB014733)).
Then, `static_array` estimates coefficients for the fixed number of the 3d B-spline functions as well as the array position averagely during each campaign through the Gauss-Newton method.
The optimial number of the 3d B-spline functions can be determined by statictical evaluation (AIC or BIC). `static_array()` function automatically returns the AIC and BIC values for a given number of the bases, and `static_array_AICBIC()` function returns the AIC and BIC values for various number of the bases.


`static_array_grad()`:

A function to estimate static array position, temporal sound speed fluctuation (NTD), and the deep gradients (in EW & NS components).
Coefficients for the fixed number of the 3d B-spline functions, the array position averagely during each campaign, and the deep gradients are estimated by the Gauss-Newton method.


`static_array_mcmcgrad()`:

A function to estimate static array position, temporal sound speed fluctuation (NTD), the shallow gradients (in EW & NS components), and the gradient depth (equal in EW & NS components).
The shallow gradients and the gradient depth are temporally constant.
These parameters and hyper-parameters controlling the observational errors are estimated by MCMC.
The method is orginally introduced in [Tomita & Kido (2022)](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-022-01740-0).
Note that this function is outdated from SeaGap version 1.1.0. and `static_array_mcmcgradv()` is recommended to be used.

`static_array_mcmcgradc()`:

A function to estimate static array position, temporal sound speed fluctuation (NTD), the shallow gradients (in EW & NS components), and the gradient depth (equal in EW & NS components).
The shallow gradients and the gradient depth are temporally constant.
The shallow gradients and the gradient depth can be optionally constrained using prior distributions.
These parameters and hyper-parameters controlling the observational errors are estimated by MCMC.
The method is orginally introduced in [Tomita & Kido (2024)](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-024-01987-9).
Note that this function is outdated from SeaGap version 1.1.0. and `static_array_mcmcgradv()` is recommended to be used.


`static_array_mcmcgradv()`:

A function to estimate static array position, temporal sound speed fluctuation (NTD), the shallow gradients (in EW & NS components), and the gradient depths (in EW & NS components).
Temporal variations of the shallow gradients and the gradient depth are modeled by 3d B-Spline functions.
The sound speed parameters expressing the NTD and the gradients are constrained by various prior distributions.
These parameters and hyper-parameters controlling the observational errors and temporal variations of sound speed parameters are estimated by MCMC.
The method is introduced in the upcoming paper.


