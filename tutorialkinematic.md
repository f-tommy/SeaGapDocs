+++
title = "Tutorials"
hascode = true
rss = "A short description of the page which would serve as **blurb** in a `RSS` feed; you can use basic markdown here but the whole description string must be a single line (not a multiline string). Like this one for instance. Keep in mind that styling is minimal in RSS so for instance don't expect maths or fancy styling to work; images should be ok though: ![](https://upload.wikimedia.org/wikipedia/en/b/b0/Rick_and_Morty_characters.jpg)"
rss_title = "Kinematic array positioning"
rss_pubdate = Date(2023, 1, 22)

tags = ["syntax", "code", "image"]
+++

# Kinematic array positioning

In SeaGap, the kinematic positioning function (`kinematic_array`) based on [Kido et al. (2006)](https://earth-planets-space.springeropen.com/articles/10.1186/BF03351996) and [Kido et al. (2008)](https://earth-planets-space.springeropen.com/articles/10.1186/BF03352785) is provided.
A kinematic array position can be estimated for each shot group by Gauss-Newton method.

\toc

## Input data

To perform `kinematic_array`, you have to prepare four input files denoted in [Dataformat](/dataformat/): "tr-ant.inp". "pxp-ini.inp", "ss\_prof.inp", and "obsdata.inp".
Outliers in "obsdata.inp" should be removed in advance; SeaGap includes a simple [outlier removal](/tutorialdenoise/) function.

In "obsdata.inp", you have to write the shot group number.

@@important
\$ head obsdata.inp
@@
```plaintext
4 4.092871 500664407.000000 107.049217 74.549984 27.432900 -44.27880 -0.45120 0.53640 500664413.190022 110.954811 76.324655 28.280988 -44.93029 -2.03165 0.89020 1 1
4 4.090700 500664437.000000 124.727831 83.456801 27.671150 -49.09920 -0.61400 1.18480 500664443.187852 127.737889 85.946412 27.083811 -50.11868 -0.25212 -1.54370 2 1
1 3.932951 500664917.000000 328.921220 253.692382 28.154000 -52.76000 -1.08760 -4.84800 500664923.030102 332.591693 256.317961 27.675444 -52.68875 -0.89626 1.21533 3 1
3 4.357953 500664917.000000 328.921220 253.692382 28.154000 -52.76000 -1.08760 -4.84800 500664923.455105 332.632065 256.226557 28.147424 -52.71458 -1.45692 -0.33433 3 1
4 4.113388 500664917.000000 328.921220 253.692382 28.154000 -52.76000 -1.08760 -4.84800 500664923.210540 332.609684 256.278857 27.880494 -52.70038 -1.13751 0.54746 3 1
1 3.915718 500664977.000000 373.032034 269.762113 26.501800 -67.98040 0.74000 1.68840 500664983.012870 378.674213 271.833273 27.468078 -71.14971 -1.07657 -0.09575 4 1
3 4.385641 500664977.000000 373.032034 269.762113 26.501800 -67.98040 0.74000 1.68840 500664983.482792 379.052958 272.008269 27.946919 -71.32463 -1.99330 0.01337 4 1
4 4.107478 500664977.000000 373.032034 269.762113 26.501800 -67.98040 0.74000 1.68840 500664983.204630 378.825441 271.905307 27.676287 -71.21717 -1.48302 -0.05251 4 1
1 3.891855 500665037.000000 433.053071 301.791614 27.998850 -102.24360 -2.02200 -0.07680 500665042.989008 438.909005 305.956022 27.407876 -104.12427 -1.15657 0.14795 5 1
3 4.431185 500665037.000000 433.053071 301.791614 27.998850 -102.24360 -2.02200 -0.07680 500665043.528337 439.555522 306.315721 27.308195 -104.49262 -0.77367 -0.75093 5 1
```

The last column shows the shot group number.
`kinematic_array()` estimates a horizontal array position using the data with each shot number if number of the observational data lines are more than `NR` (`NR=3` by default, but you cannot assign `NR`<2 because number of unknown paramters is 3).
The detailed format of "obsdata.inp" are shown in [Dataformat](/dataformat/).


## Estimation

You can simply perform `kinematic_array` as following: 

```julia
lat=36.15753; TR_DEPTH=[4.0]
SeaGap.kinematic_array(lat,TR_DEPTH)
```

The input files are `fn1`, `fn2`, `fn3`, and `fn4`; they correspond to "tr-ant.inp". "pxp-ini.inp", "ss\_prof.inp", and "obsdata.inp" by default.

Then, after performing, you obtained a log file as `fno0` (`fno0="log.txt"` by default), a position file `fno1` (`fno1="kinematic_array.out"` by default), and a data misfit file `fno2` (`fno2="residual_kinematic.out"`).

"kinematic\_array.out"
* 1: the transmitting time [sec]
* 2: number of the used observational data for each shot group
* 3: Easting displacement [m]
* 4: Northing displacement [m]
* 5: Vertical displacement [m]
* 6: NTD [sec]
* 7: Std. of Easting disp. [m]
* 8: Std. of Northing disp. [m]
* 9: Std. of Vertical disp. [m]
* 10: Std. of NTD [sec]
* 11: the shot group number
Note that 7-9 columns show "Inf" if number of the used observational data is 3.

@@important
 \$ head kinematic\_array.out
@@
```plaintext
5.00664917e8 3 0.15662931734126728 -0.08084253543354349 0.0 0.0019649922034895954 Inf Inf 0.0 Inf 3
5.00664977e8 3 0.13940031191470945 -0.044998764987785926 0.0 0.001975743839794656 Inf Inf 0.0 Inf 4
5.00665037e8 3 0.09543873587180635 -0.04340700740876836 0.0 0.0019599866103748153 Inf Inf 0.0 Inf 5
5.00665097e8 3 0.04579833896120086 0.006630443387361995 0.0 0.0019531607198649916 Inf Inf 0.0 Inf 6
5.00665157e8 4 0.06508724467893012 0.04206545454468572 0.0 0.0020172767856484557 0.00082667431544421 0.0008053431612205779 0.0 3.337355744065251e-7 7
5.00665217e8 4 0.06519260104146817 0.07061473998724858 0.0 0.0019966098658202587 0.03206491027644252 0.029742444781263446 0.0 1.322217043643957e-5 8
5.00665277e8 4 0.1133555414810341 0.09020348130701686 0.0 0.0019861055990347963 0.0677753402250701 0.06004930873573364 0.0 2.8314571352981234e-5 9
5.00665337e8 3 -0.02806919533739579 0.15267116520607066 0.0 0.0019312015121429856 Inf Inf 0.0 Inf 10
5.00665397e8 4 0.06272950313565163 0.08333257777799022 0.0 0.001982007884391134 0.050622346741337775 0.042713667410086316 0.0 2.1775176525718885e-5 11
5.00665457e8 4 0.12269481489808792 0.0679794473962017 0.0 0.001975411460083794 0.04764292338782072 0.03874236803802223 0.0 2.1044695812855657e-5 12
```

"residual\_kinematic.out"
* 1: the transmitted time
* 2: the transponder number
* 3: misfit [sec]
* 4: number of interations
* 5: the shot group number
* 6: number of the used data
If the number of the used data is 3, the misfits are almost zero.

@@important
\$ head -n 16 residual\_kinematic.out
@@
```plaintext
5.00664917e8 1 -1.734723475976807e-18 3 3 3
5.00664917e8 3 4.336808689942018e-18 3 3 3
5.00664917e8 4 -3.903127820947816e-18 3 3 3
5.00664977e8 1 -8.673617379884035e-19 3 4 3
5.00664977e8 3 6.071532165918825e-18 3 4 3
5.00664977e8 4 -4.336808689942018e-18 3 4 3
5.00665037e8 1 4.0332320816460765e-17 3 5 3
5.00665037e8 3 -1.1839487723541708e-16 3 5 3
5.00665037e8 4 1.214306433183765e-17 3 5 3
5.00665097e8 1 -2.6020852139652106e-18 3 6 3
5.00665097e8 3 3.903127820947816e-18 3 6 3
5.00665097e8 4 2.6020852139652106e-18 3 6 3
5.00665157e8 1 1.9521010603167424e-7 3 7 4
5.00665157e8 2 -2.49146658609211e-7 3 7 4
5.00665157e8 3 2.9776429482687716e-7 3 7 4
5.00665157e8 4 -2.4382774225020776e-7 3 7 4
```
 

You can change the following inversion criterion values by keyword arguments:
* `eps`: Convergence criteria [m], the default is 1.e-4 (each inversion is converged when RMS of difference between the previous and the new solutions of the horizontal array displacements < `eps`)
* `ITMAX`: Maximum number of interations, the default is 20
* `delta_pos`: Infinitesimal amount of the array positions to calculate the Jacobian matrix, the default is 1.e-4 [m]

For example:
```julia
SeaGap.kinematic_array(lat,TR_DEPTH,delta_pos=1.e-5)
``` 

If you'd like to calculate an average position during a campaign  from the positioning results "kinematic\_array.out", you can use `position_kinematic(;fn,weight,fno)`.
`fn` is the input file name (`fn="kinematic_array.out"` by default), and `fno` is the outpt file name `fno="position_kinematic.out"`.
If `weight=true`, weighted mean position is calculated using the shot groups with number of shot data for each group >= 4 (`weight=false` by default); thus, you cannot set `weight=true` for a GNSS-A site with only three transponders.

For example:
```julia
SeaGap.position_kinematic(fn="kinematic_array.out",weight=false)
```

"position\_kinematic.out"
* 1: the mean time [sec]
* 2: EW mean array displacement [m] 
* 3: NS mean array displacement [m] 
* 4: UD mean array displacement [m] **Not available**
* 5: Std of EW array displacement [m] 
* 6: Std of NS array displacement [m] 
* 7: Std of UD array displacement [m] **Not available**

@@important
\$ cat position\_kinematic.out
@@
```plaintext
5.006910713024619e8 0.02405891197862978 -0.00999109884151074 NA 0.04738812642214625 0.0592217059058522 NA
```

You can estimate uplift component using `kinematic_arra_3d()`. In this case, you need to add option `updown=true` when using `position_kinematic()`.

## Visualization 

If you'd like to plot the array positions in a horizontal map, you can use `plot_map_array_kinematic(xrange,yrange; autoscale,fn,fno,show,col_num)` function.
* If `autoscale=true` (default), the plot range is automatically determined. If `autoscale=false`, the plot range is fixed by `xrange` and `yrange`.
* `fn` is the input file name: "array\_kinematic.out" by default.
* If `show=false`, the figure is saved as `fno` (`fno` is name of the output figure). If `show=true` in REPL, a figure is temporally shown.
* If `col_num=1` (default), the plot is colored by the observation time. If `col_num=2`, the plot is colored by number of the used observational data. If `col_num=0`, the plot is colored by blue. 

```julia
SeaGap.plot_map_kinematic_array((-0.5,0.5),(-0.5,0.5),autoscale=false,fno="map_array_kinematic.png")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/map_array_each.png" width="300" height="300">
    <div style="clear: both"></div>      
  </div>
</div>
~~~

If you'd like to plot the array positions in time-series, you can use `plot_time_kinematic_array(EW_range,NS_range.ntdrange.autoscale,fn,fno,show)` function.
* If `autoscale=true` (default), the plot range is automatically determined. If `autoscale=false`, the plot range of y-componet is fixed by `EW_range`, `NS_range`, and `ntdrange`. The range of x-compoent (Time) is automatically determined in the both cases.
* `fn` is the input file name: "kinematic\_array.out" by default.
* If `show=false`, the figure is saved as `fno` (`fno` is name of the output figure). If `show=true` in REPL, a figure is temporally shown.


```julia
SeaGap.plot_time_kinematic_array(fno="time_kinematic_array.pdf")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/time_array_each.png" width="450" height="450">
    <div style="clear: both"></div>      
  </div>
</div>
~~~

## Outlier elimination

If you'ld like to eliminate outlier from the positioning results "kinematic\_array.out", SeaGap provides `denoise_kinematic(;method,n,sigma1,sigma2,type,save,prompt,fn,fn0,fno)` as similar with `denoise()` in [Outlier removal](/tutorialdenoise/). `denoise_kinematic()` peform two types of filters; one is a spatial filter, and the other is a temporal filter.

In the spatial filter, we first calculate a mean/median position of the horizontal array positions, and then calculate standard deviation.
The horiontal array positions beyond the range of `sigma1`\*(Std) are eliminated as outliers.

In the temporal filter, we first perform a running mean/median filter to time-series of the horizontal array positions; then, calcualte standard deviation of the residuals after removing the filtered array positions.
The residuals after removing the filtered array positions beyond the range of `sigma2`\*(Std) are eliminated as outliers.

If you'd like to perform both filters, you set `type="both"`.
You can set `type=spatial` or `type=temporal` if you'd like to perform only one of the filters.
`method` selects "mean" or "median".

Other arguments are used as following:
* `fn`: Input file name
* `fno1`: Output figure name (`fno1="denoise_kinematic.pdf"` by default)
* `fno2`: Output file which lists the eliminated shot numbers
* `n`: Window size for the running filter
* `save`: if `save=true`, the input data file `fn` is renamed and rewritten in `fn0` (`save=true` by default)
* `prompt`: if `prompt=true`, confirmation message is shown; if false, the input file is forcely rewritten (`prompt=true` by default)  

You can adjust a figure shown by this function can be adjusted by the other additional arguments.

If you perform `denoise_kinematic` as following:
```julia
SeaGap.denoise_kinematic(n=7,sigma1=4.0,sigma2=5.0,method="median")
```

the following figure is shown.

~~~
<div class="row">
  <div class="container">
    <img src="/assets/denoise_each.png" width="350" height="350">
    <div style="clear: both"></div>
  </div>
</div>
~~~

Then, the following message is shown if `prompt=true`:
```plaintxt
  Do you accept the denoise processin?
```

If you accept the elimination, you enter "yes"; then, the outliers (red symbols) are eliminated from "kinematic\_array.out" and the outlier list `fno2` is saved:

@@important
\$ cat eliminated\_list.out
@@
```plaintext
59
61
62
63
64
71
26
86
87
104
57
77
79
```

These correspond to the 9th colmun of "kinematic\_array.out".

If you enter "no", "kinematic\_array.out" is not updated.

