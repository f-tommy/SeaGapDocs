+++
title = "Tutorials"
hascode = true
rss = "A short description of the page which would serve as **blurb** in a `RSS` feed; you can use basic markdown here but the whole description string must be a single line (not a multiline string). Like this one for instance. Keep in mind that styling is minimal in RSS so for instance don't expect maths or fancy styling to work; images should be ok though: ![](https://upload.wikimedia.org/wikipedia/en/b/b0/Rick_and_Morty_characters.jpg)"
rss_title = "MCMC static array positioning"
rss_pubdate = Date(2023, 1, 22)

tags = ["syntax", "code", "image"]
+++

# MCMC static array positioning

\toc

## Make initial value file

In order to perform the static array positioning considering a sound speed gradient through a MCMC technique ([Tomita & Kido, 2022](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-022-01740-0)), we have to prepare an initial value file (named "initial.inp" by default) which lists initial values of the unknown parameters for MCMC optimization.

The dataformat of "initial.inp" is (Columns 1: Initial value, 2: Lower limit, 3: Upper limit, 4: Step width, 5: Parameter name).
The step width is used as perturbation when new parameter candidates are produced in the MCMC sampling. This should be appropriately given to effectively perform a MCMC sampling.
 
The type of unknown parameters to be placed on each line of this file is fixed and cannot be changed.
* Lines 1-3: Array displacements for EW, NS, UD [m]
* Lines 4-5: Shallow gradients for EW, NS [sec/km]
* Line 6: Gradient depth [km]
* Lines 7-11: Long-term NTD modeled by 4th polynomial function (degree: 0-4)
* Lines 12-13: Scale factor optimizing observational errors
* Lines 14-: Coefficinets for the 3d B-spline bases (Short-term NTD)

This file can be produced by `make_initial(fn1,fn2,fn3,fno,error_scale,tscale)` function.
`fn1` is the initial seafloor transponder positions: "pxp-ini.xyh".
`fn2` and `fn3` are "residual.out" and "solve.out", respectively; they are obtained by [the basic static array positioning](/tutorialstatic/) `pos_array_all()`.

`error_scale` is used for providing the step width (`error_scale=5.0` by default).
Then, `make_initial()` provides the step widths as ($1\sigma$ standard deviation / `error_scale`) for the parameters at Lines 1-3, 7-11, and 14-.

`tscale` is used to normalize time when obataining L-NTD parameters as $\sum_{m=0}^{4} \gamma_{m}t^m_n/(60*60*{\rm tscale})$ in [Methodology](/methodmcmcpvg/).
Since `t` indicates the observational time in second, $60*60$ converts the time into the unit of hour.
In order to promote the acceptance ratio of the MCMC sampling, `tscale` should be set to the whole observational period [hour].

 
```julia
SeaGap.make_initial(fn1="pxp-ini.xyh",fn2="residual.out",fn3="solve.out",fno="initial.inp",error_scale=5.0,tscale=10.0)
```

@@important
\$ head -n 20 initial.inp
@@
```plaintext
0.025636280922571503 -20.0 20.0 0.0003285573488282583 EW_disp.
-0.015945415334263136 -20.0 20.0 0.0003275385835573224 NS_disp.
-0.0341679281482854 -20.0 20.0 0.0012002522323013415 UD_disp.
0.0 -20.0 20.0 1.e-6 S-Gradient_EW
0.0 -20.0 20.0 1.e-6 S-Gradient_NS
0.65 0 2.7056096662950706 0.01  Gradient_depth
0.0019017563284706718 -20.0 20.0 1.6611016486294932e-6 L-NTD_1
-0.0007901676224344456 -20.0 20.0 1.5449119464738023e-5 L-NTD_2
0.0015766489100226266 -20.0 20.0 4.222747834588211e-5 L-NTD_3
-0.0012713154100225665 -20.0 20.0 4.290762653134229e-5 L-NTD_4
0.0005980067084534468 -20.0 20.0 1.4451169310092645e-5 L-NTD_5
-4.0 -20.0 20.0 0.005 Scale_1
-3.5 -20.0 20.0 0.005 Scale_2
0.0013391985795993162 -20.0 20.0 5.925901087601794e-5 S-NTD_1
0.0019548158709784824 -20.0 20.0 1.2467626341387438e-5 S-NTD_2
0.0019695006501768662 -20.0 20.0 4.302502267822001e-6 S-NTD_3
0.001779083361610449 -20.0 20.0 2.830791012520501e-6 S-NTD_4
0.002088654982500453 -20.0 20.0 2.596471958417347e-6 S-NTD_5
0.0020800544363949007 -20.0 20.0 2.475559611199917e-6 S-NTD_6
0.002210865507757804 -20.0 20.0 2.4503884543907966e-6 S-NTD_7
```

If you provide same values for the columns 1-3 at a certain line, the corresponding parameter is handled as a constant parameter.
For example, when you provide "0.65" for the columns 1-3 at the 6th line, you can estimate an array displacement with the gradient depth is fixed to 650 m.


## Run MCMC processing

Before you run `static_array_mcmcgrad()`, you have to set number of threads for the thread parallelization in your terminal as:

@@important
\$ export JULIA\_NUM\_THREADS=4
@@

or you have to run Julia as:

@@important
\$ julia -t 4
@@

This value is determined depending on your machine ability.

Then, after setting the above thread number and preparing the above input files, you can perform `static_array_mcmcgrad(lat,TR_DEPTH,NPB; NSB,nloop,nburn,ndelay,NA,lscale,daave,daind,tscale,fn1,fn2,fn3,fn4,fn5,fno0,fno1,fno2,fno3,fno4,fno5,fno6,fno7)` function as following:

```julia
lat=36.15753; TR_DEPTH=4.0; NPB=73
SeaGap.static_array_mcmcgrad(lat,TR_DEPTH,NPB,NSB=50,aventd=true,daave=2.5e-6,daind=3.5,lscale=10.0)
```

Meanings of the arguments of `static_array_mcmcgrad()` are following:
* `lat`: Latitude of the site
* `TR_DEPTH`: Depth of the sea-surface transducer from the sea-surface [m]
* `NPB`: Number of the 3d B-spline bases (this should be optimized in the conventional static array positioning `pos_array_all_AICBIC()`)
* `NSB`: Number of the perturbated B-spilne bases for each iteration (`NSB=100` by default)
* `nloop`: Total number of the MCMC iterations (1200000 by default)
* `nburn`: Burn-in period of the MCMC iterations (samples less than `nburn` is excluded from the final results; 200000 by default)
* `ndelay`: Number of the MCMC iterations for starting to perturb the scaling parameters (`ndelay=1` by default)
* `NA`: Number of the sampling interval (5 by default; if you set (`nloop=1200000`, `nburn=200000`, and `NA=5`), you can obtain (1200000-200000)/5 samples)
* `lscale`: Scaling factor for the step width of the long-term NTD parameters (`lscale=1.0` by default)
* `aventd`: Provide common perturbation to all S-NTD parameters when `aventd=true` (`aventd=false` by default)
* `daave`: Step width for average NTD when (`aventd=true`)
* `daind`: Scaling dactor for step width of individual NTD when (`aventd=true`)
* `tscale`: Temporal scaling for time in the polynomial functions (time [sec] is converted into [hour]/`tscale`, `tscale=10` by default)
* `fn1-5`: the input file names ("tr-ant.inp", "pxp-ini.inp", "ss\_prof.inp", "obsdata.inp", and "initial.inp")
* `fno0`: the log file ("log.txt" by default)
* `fno1-7`: the output file names ("sample.out", "mcmc.out", "position.out", "statistics.out", "acceptance.out", "residual\_grad.out", and "bspline.out")

### Delay to start updating for the scaling factors 
The function has some options to enhance the acceptance ratio.
Although the function optimizes the scaling factors for the two observation equations shown in [Methodology](/methodmcmcpvg/), these values sometimes fix before convergence of the other unknown parameters.
In such case, `ndelay` should be set to certain value (such as half of the burn-in period).
By this option, optimization of the scaling factors starts after `ndelay` iterations.

### Scaling for the step widths for L-NTD parameters
Due to the strong covariance among the L-NTD parameters, they tends to decrease the acceptace ratio.
In this case, `lscale` reduces the step widths of the L-NTD parameters.
If `lscale=10`, the step width for each L-NTD parameter is a tenth of the original value.
Moreover, `tscale` is also important to increase the acceptance ratio; `tscale` used in `make_initial()` must be given in `tscale` here.

### Partial update for coefficients of 3d B-spline functions
At each MCMC iteration, not all parameters on the 3d B-spline bases are changed to increase the acceptance ratio.
Number of the changed parameters on the 3d B-spline bases is controlled by `NSB`.

### Average update for coefficients of 3d B-spline functions
If observational data has enough sensitivity to solve a vertical array displacement, it has a trade-off relationship with the average of NTDs.
In such a case, the acceptance ratio degrades, and the MCMC sampling fail to express the true posteior PDF on the vertical array displacement.

A simple way to solve this problem is a uniform update on coefficients for 3d B-spline functions $c_j$ in Equation (5) of [Methodology](/methodmcmcpvg/).
This updation is performed when `aventd=true`.

When `aventd=false`, a coefficient for the $j$th 3d B-spline basis is updated as:

$$ c'_j=c_j + u\sigma_{c_j} $$

where $u$ is a random value defrived from a uniform distribution [-0.5,0.5], and $\sigma_{c_j}$ is a step width for this parameter given in "initial.inp".
By contrast, when `aventd=true`, a coefficient for the $j$th 3d B-spline basis is updated as:

$$ c'_j=c_j + u_{\rm com}\sigma_{\rm com} + u\sigma_{c_j}/\Delta_{c} $$

$u_{\rm com}$ a random value defrived from a uniform distribution [-0.5,0.5], but it is a common value among the coefficients at each updation.
$\sigma_{\rm com}$ is a step width for whole 3d B-spline functions.
Thus, $u_{\rm com}\sigma_{\rm com}$ give a perturbation to the average level of the 3d B-spline functions.
While, $u\sigma_{c_j}$ give a pertubation to the individual coefficient for the  3d B-spline basis, and $\Delta_{c}$ is a scaling factor to encourage acceptance by providing $\Delta_{c}>1$.

$\sigma_{\rm com}$ and $\Delta_{c}$ can be given as `daave` and `daind`, respectively. 

## Output text files

@@importantv
fno0: the log file, which shows the analytical conditions, and so on
@@

@@important
\$ head log.txt
@@
```plaintext
2023-01-17T19:00:41.919
static_array_mcmcgrad.jl at /Users/test_PC/SeaGap_test/mcmc
Number of threads: 4
Number_of_B-spline_knots: 77
Default_latitude: 36.15753
TR_DEPTH: 4.0
Number_of_MCMC_loop: 1200000
Burn_in_period: 200000
Sampling_interval: 5
Scale: true
```

@@importantv
fno1: The sampled unknown parameters ("sample.out" by default)
@@

In this file, the unknown parameter names written in "initial.inp" is written at the first line.
Then, the sampled parameters for each MCMC iteration at each line.

@@important
\$ head sample.out | awk '{print \$1,\$2,\$3,\$4,\$5,\$6}'
@@
```plaintext
EW_disp. NS_disp. UD_disp. S-Gradient_EW S-Gradient_NS Gradient_depth
0.0792280816606764 -0.07808502829846697 -0.038635864431547984 -8.68250555312698e-5 0.0001083565550747744 1.2313132109702847
0.0792280816606764 -0.07808502829846697 -0.038635864431547984 -8.68250555312698e-5 0.0001083565550747744 1.2313132109702847
0.0792280816606764 -0.07808502829846697 -0.038635864431547984 -8.718446946801919e-5 0.00010799325700068288 1.2313132109702847
0.0792280816606764 -0.07808502829846697 -0.038635864431547984 -8.649523108069255e-5 0.00010798884128099522 1.2313132109702847
0.0792280816606764 -0.07808502829846697 -0.038635864431547984 -8.649523108069255e-5 0.00010798884128099522 1.2313132109702847
0.07951942860412713 -0.07842957364617842 -0.03781991489286092 -8.733991904332083e-5 0.00010881386462605156 1.2344368074146146
0.0797973876496036 -0.07865097632952621 -0.038638614141646405 -8.643812294091133e-5 0.00010794655607095396 1.230610894350092
0.0797973876496036 -0.07865097632952621 -0.038638614141646405 -8.738808866230476e-5 0.00010699597479892664 1.230610894350092
0.0797973876496036 -0.07865097632952621 -0.038638614141646405 -8.755999059925743e-5 0.00010683959008568226 1.230610894350092
```

@@importantv
 fno2: The PDFs and RMS ("mcmc.out" by default)
@@

In this file, the PDF and RMS values for each MCMC iteration are recorded.
- 1: Iteration number
- 2: Process number (0 and 1 corresponds to optimization for observation equations 1 and 2, respectively)
- 3: Acceptance (0 and 1 are reject and accept the MCMC perturbation, respectively)
- 4: The posterior PDF for the observation equation 1
- 5: The posterior PDF for the observation equation 2
- 6: The total posterior PDF
- 7: RMS for the observation equation 1
- 8: RMS for the observation equation 2

@@important
\$ head mcmc.out
@@
```plaintext
5 1 1 30640.5316249358 26757.182836699594 57397.71446163539 3.419040965749844e-5 0.00012051927477698287
10 0 0 30644.022168433235 26813.7289314623 57457.75109989554 3.429663404938296e-5 0.0001175853809320721
15 1 1 30644.02943515391 26821.252387755638 57465.28182290954 3.429600320700134e-5 0.00011634444275541483
20 0 1 30687.95350102651 26823.489752791196 57511.443253817706 3.449121949976427e-5 0.00011579450769642835
25 1 0 30687.992457113745 26871.354381771493 57559.346838885234 3.44879605037534e-5 0.00011191537288709573
30 0 1 30704.209918043824 26886.500131302717 57590.71004934654 3.466740851167605e-5 0.00011215778277425875
35 1 1 30733.804150874275 26917.628966105163 57651.43311697944 3.430833898450772e-5 0.00011717803812660892
40 0 0 30733.799521863253 26943.733104792474 57677.532626655724 3.430871686462821e-5 0.00011672459488211255
45 1 0 30751.288984391485 26975.74207282055 57727.031057212036 3.435878909575146e-5 0.00011265293645483022
50 0 0 30772.365768478183 26975.302681107092 57747.668449585275 3.4477732396622186e-5 0.00011275388395079637
```

@@importantv
fno3: The position file ("position.out" by default)
@@

This is same format with the "position.out" of `pos_array_all()`.
(1: the cumlative seconds of the observational time, 2-4: Mean array displacements for EW, NS, UD componets, 5-7: Standard deviations of 2-4)

@@important
\$ cat position.out
@@
```plaintext
5.006911464354839e8 0.078367613458201 -0.08514945576712399 -0.0340102206482122 0.005144727208048865 0.0062974579205844075 0.005704865959222425
```

@@importantv
fno4: Statistical values for each unknown parameter ("statistics.out" by default)
@@

@@important
\$ head statistics.out
@@
```plaintext
#Parameter mean std median min max
 "EW_disp."         0.078367613458201       0.005144727208048865     0.07784887468370676     0.06576224682341979      0.09457305348507103
 "NS_disp."        -0.08514945576712399     0.0062974579205844075   -0.08511206694720905    -0.10587183701850005     -0.06705651655040301
 "UD_disp."        -0.0340102206482122      0.005704865959222425    -0.03399846412638977    -0.05436245652909059     -0.011735357475848841
 "S-Gradient_EW"   -8.62246678447068e-5     3.0006948844345516e-6   -8.618574116032473e-5   -9.955275906691991e-5    -7.521556344838198e-5
 "S-Gradient_NS"    0.00011325398201524354  3.86941849574838e-6      0.0001134357635898253   9.664595891720896e-5     0.0001267846621482411
 "Gradient_depth"   1.2761646163708869      0.11917931187797215      1.2623998589766008      0.966240303135601        1.7100642291626729
 "L-NTD_1"          0.0018826733230646996   1.631740677798977e-5     0.0018779421385805451   0.0018583776140325026    0.0019242964772991215
 "L-NTD_2"          1.5164726762837501e-5   0.0001486215962420044    6.485270965536119e-5   -0.0003530570926899982    0.0002029738274772864
 "L-NTD_3"         -0.00027510582677679063  0.0004028444295857216   -0.0004212020126622335  -0.0007469938247558138    0.0006869512725884606
```

@@importantv
fno5: Acceptance ratio ("acceptance.out" by default)
@@

The first and second lines show the acceptance ratios for the observation equations 1 and 2, respectively.

@@important
\$ cat acceptance.out 
@@
```plaintext
Acceptance_ratio_MCMC-1: 0.2813833333333333
Acceptance_ratio_MCMC-2: 0.28363333333333335
```

Note that it is plausible to adjust the pertubation widths as the acceptance ratio is ~23 % when sampling multiple parameters ([Gelman et al., 1996](https://global.oup.com/academic/product/bayesian-statistics-5-9780198523567?cc=jp&lang=en&#))

@@importantv
fno6: Data residuals including the NTD and gradient effects ("residual\_grad.out" by default)
@@

The effects of the modeled NTD and gradients on the data residuals are shown.
- 1: The cumulative seconds of the observational time
- 2: Seafloor transponder number
- 3: EW sea-surface platform positions
- 4: NS sea-surface platform positions
- 5: UD sea-surface platform positions
- 6: Projected travel-time residuals ($\frac{1}{M\left(\xi_{n,k}\right)} T_{n,k}^{\mathrm{obs}}-\frac{1}{M\left(\xi_{n,k}\right)} {T}^{\mathrm{cal}}\left(\mathbf{p}_k+\delta \mathbf{p}, \mathbf{u}\left(t_{n},{\bf b}_0\right), v_0\right)$)
- 7: The modeled NTD by 3d B-spilne function ($\sum_{j=1}^{J}c_j\Phi_j(t_n)$)
- 8: Effects of the deep gradients ($\frac{D}{2}\mathbf{g}_{\mathrm{s}}\mathbf{h}_{n,k}$)
- 9: The modeled travel-time in the observation equation 1 (7+8)
- 10: The long-term NTD modeled by 4th polynomial funcions ($\sum_{m=0}^{4} \gamma_{m}t^m_n$)
- 11: Effects of the shallow gradients ($\mathbf{g}_{\mathrm{s}} \mathbf{u}^{\mathrm{hor}}\left(t_{n}\right)$)
- 12: The modeled travel-time in the observation equation 2 (10+11)
- 13: The final travel-time residuals (6-9)

@@important
\$ head residual\_grad.out
@@
```plaintext
 5.00664410095011e8    4.0     96.89902643051431        88.03270369967535      15.953261659441749  0.0017979333422704167  0.0018584604480744815  -4.5251727191123945e-5   0.0018132087208833575  0.0018826746247814269   1.6149678731097327e-6   0.0018842895926545367  -1.527537861294089e-5
 5.00664440093926e8    4.0    113.06319366902844        96.4390767559066       15.728498650814862  0.0018355095168310143  0.0018711757256623697  -4.5152091136073423e-5   0.0018260236345262963  0.00188268703141493     1.1732731449064043e-6   0.0018838603045598363   9.48588230471805e-6
 5.00664920015051e8    1.0    317.258830646345         266.20104706095884      16.12337475425815   0.0019492644996791438  0.0019535287641539263   5.280610901181443e-6    0.001958809375055108   0.00188283456345753     2.7927913029996435e-6   0.0018856273547605297  -9.54487537596393e-6
 5.0066492022755253e8  3.0    317.4171843638199        266.24101689269014      16.27763781567538   0.001896763873769342   0.00195352481786977    -6.6592942217753765e-6   0.0019468655236479946  0.0018828346079298765   2.783664048912453e-6    0.001885618271978789   -5.0101649878652764e-5
 5.0066492010527e8     4.0    317.3272458725733        266.2184268441425       16.190149350893314  0.0019459624690290592  0.0019535270920768163  -4.5522462116032325e-5   0.0019080046299607839  0.001882834582340793    2.788860552494706e-6    0.001885623442893288    3.7957839068275224e-5
 5.0066498000643504e8  1.0    359.35661904162504       277.1735513446349       15.411855263006803  0.001964701328794626   0.001951368659445891    5.834976455921046e-6    0.0019572036359018124  0.0018828464000669632   4.056032844255083e-7    0.0018832520033513886   7.497692892814044e-6
 5.00664980241396e8    3.0    359.62420167355594       277.19483358357854      15.511658046421838  0.001899893275194323   0.001951356235912045   -6.095318608697708e-6    0.0019452609173033473  0.001882846443597678    3.8494135917281093e-7   0.0018832313849568509  -4.5367642109024196e-5
 5.00664980102315e8    4.0    359.467298440242         277.18216826356564      15.453882681451681  0.0019338113118865475  0.0019513635933835552  -4.497496940807374e-5    0.0019063886239754815  0.0018828464178330987   3.970358904241129e-7    0.0018832434537235229   2.7422687911066072e-5
 5.00665039994504e8    1.0    418.9642018513322        300.25882611368763      15.696507359928955  0.0019360458657012283  0.0019472760245312756   6.419280227790985e-6    0.0019536953047590666  0.0018828568007591216  -2.119541450856093e-6    0.0018807372593082656  -1.7649439057838284e-5
 5.006650402641685e8   3.0    419.2394061616972        300.4615189327145       15.708606565510767  0.0019039860680888346  0.0019472536413713678  -5.503913368437491e-6    0.0019417497280029303  0.001882856844287782   -2.120315082226064e-6    0.001880736529205556   -3.7763659914095765e-5
```

@@importantv
fno7: ("bspline.out" by default)
@@

Format of this file is same  with that of `pos_array_all()`.

@@important
\$ head bspline.out
@@
```plaintext
1 1 5.006636573280344e8 0.001298067358242476
2 2 5.00664407e8 0.0019696571745976103
3 3 5.006651566719656e8 0.0019658305782191935
4 4 5.006659063439312e8 0.0017753643399709875
5 5 5.006666560158968e8 0.0020988732080715793
6 6 5.006674056878624e8 0.0020949154531169916
7 7 5.00668155359828e8 0.002228873405588681
8 8 5.006689050317936e8 0.0018684765371399623
9 9 5.006696547037592e8 0.0017853297206791739
10 10 5.006704043757248e8 0.0016702469705376871
```
                                                                                    
## Visualization

SeaGap prepares various functions to visualize results of the MCMC processing.

### Posterior PDF and RMS variation through the MCMC iteration

When you'd like to check how the posterior PDF and the travel-time RMS change through the MCMC iteration, you can draw by `plot_mcmcres(;fn,fno,show,nshuffle)`. 
* fn: the input file name (`fn="mcmc.out"` by default)
* fno: Output figure name (`fno="mcmc_res.pdf"` by default)
* show: if `show=true`, a figure is shown on REPL and is not saved as a file (`show=false` by default)
* nshuffle: number of plots (if all samples are plotted, the figure is crowded; thus, `nshuffle` of samples are randomly picked; if `nshuffle=0`, all samples are plotted; `nshuffle=50000` by default)
You can adjust the fontsizes or figuresize by the other arguments.

```julia
SeaGap.plot_mcmcres(fno="mcmc_res.pdf")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/mcmc_res.png" width="350" height="350">
    <div style="clear: both"></div>
  </div>
</div>
~~~

### Parameter variation through the MCMC iteration

When you'd like to check how the unknown parameters change through the MCMC iteration, you can draw by `plot_mcmcparam(NA;fn,fno,show,nshuffle)`.
* NA: Sampling interval of the MCMC prcessing (`NA=5` by default), which must be same with `NA` in `pos_array_mcmcpvg`
* fn: the input file name (`fn="sample.out"` by default)
* fno: Output figure name (`fno="mcmc_param.pdf"` by default)
* show: if `show=true`, a figure is shown on REPL and is not saved as a file (`show=false` by default)
* nshuffle: number of plots for each parameter (if all samples are plotted, the figure is crowded; thus, `nshuffle` of samples are randomly picked; if `nshuffle=0`, all samples are plotted; `nshuffle=10000` by default)
You can adjust the fontsizes or figuresize by the other arguments.

```julia
SeaGap.plot_mcmcparam(5,fno="mcmc_param.pdf")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/mcmc_param.png" width="600" height="600">
    <div style="clear: both"></div>
  </div>
</div>
~~~

In this function, major six unknown parameters are shown.
If you'd like to draw a figure for the other paramters, you can use `plot_mcmcparam_each(param,NA;fn,fno,show,nshuffle)`.
`param` is the parameter name written in "initial.inp".

```julia
SeaGap.plot_mcmcparam_each("S-NTD_10",5,fno="mcmc_param_S-NTD_10.png")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/mcmc_param_S-NTD_10.png" width="150" height="150">
    <div style="clear: both"></div>
  </div>
</div>
~~~

### Correlation among the unknown parameters

When you'd like to simply check the trade-off relationship among the unknown parameters, you can draw a correlation map by `plot_cormap(fn,fno0,fno,show,txt,all,as,pfs,plot_size)`.
* fn: the input file name (`fn="sample.out"` by default)
* fno0: Output text data file name (if `txt=true`, correlation coefficients are save in this file; `fno0="correlation.out"`)
* fno: Output figure name (`fno="cormap.pdf"` by default)
* show: if `show=true`, a figure is shown on REPL and is not saved as a file (`show=false` by default)
* txt: Text data file output (`txt=false` by default)
* all: if `all=true`, correlation coefficients are calculated for all parameters; if `all=false`, correlation coefficients are calculated for major paramters (array displacements, shallow gradients, gradient depth)
* as: Fontsize of parameter names (`as=10` by default)
* pfs: Fontsize of annotated correlation values (`pfs=8` by default)
* plot\_size: Figure size (`plot_size=()` by default)

```julia
SeaGap.plot_cormap(fno="cormap.png")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/cormap.png" width="450" height="450">
    <div style="clear: both"></div>
  </div>
</div>
~~~

If you set as following:

```julia
SeaGap.plot_cormap(all=true,fno="cormap_all.png",plot_size=(1400,1400),txt=true)
```

you obtain the below figure and the output text data

~~~
<div class="row">
  <div class="container">
    <img src="/assets/cormap_all.png" width="450" height="450">
    <div style="clear: both"></div>
  </div>
</div>
~~~

@@important
\$ head -n 6 correlation.out | awk \'{print \$1,\$2,\$3,\$4,\$5,\$6}\'
@@
```plaintext
Name: EW_disp. NS_disp. UD_disp. S-Gradient_EW S-Gradient_NS
"EW_disp." 1.0 -0.634376617642439 -0.09873898084769465 -0.4559821875070524 -0.08852812940100827
"NS_disp." -0.634376617642439 1.0 0.07959031864890413 -0.055760129578559775 -0.36041442138448465
"UD_disp." -0.09873898084769465 0.07959031864890413 1.0 -0.026356064178314775 0.1619929930250248
"S-Gradient_EW" -0.4559821875070524 -0.055760129578559775 -0.026356064178314775 1.0 -0.04550428034648942
"S-Gradient_NS" -0.08852812940100827 -0.36041442138448465 0.1619929930250248 -0.04550428034648942 1.0
```

You can see the strong correlation between the horizontal array displacements and the gradient depth, and between the vertical array displacement and NTDs.


### Histogram for each unknown parameter

From the MCMC samples ("sample.out"), you can draw a histogram for each unknown parameter by `plot_histogram(NPB,fn0,fn,fno,all,drawnls,nbins)`.
* NPB: Number of 3d B-spline bases
* fn0: Inversion results by `pos_array_all()` ("solve.out" by default), which is used when you'd like to plot the `pos_array_all()` results on the histogram
* fn: Input file ("sample.out" by default)
* fno: Output figure name (note that this file must be a PDF file: "histogram.pdf" by default)
* all: if `all=true`, histograms for all parameters are drawn; if `all=false`, histograms for major six parameters (array displacements, shallow gradients, gradient depth) (`all=false` by default)
* drawnls: if `drawnls=true`, a normal distribution estimated by `pos_array_all()` is drawn in a histogram (`drawnls=false` by default); the normal distributions are shown for the array displacements and 3d B-spline NTDs
* nbins: Number of histogram's intervals (`nbins=50` by default)

```julia
SeaGap.plot_histogram(77,all=true,fno="histogram_all.pdf")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/histogram_all.jpg" width="400" height="400">
    <div style="clear: both"></div>
  </div>
</div>
~~~

```julia
SeaGap.plot_histogram(77,all=true,fno="histogram_allv.pdf",drawnls=true)
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/histogram_allv.jpg" width="400" height="400">
    <div style="clear: both"></div>
  </div>
</div>
~~~

### Histograms and heatmaps for multiple parameters 

Using `plot_histogram2d(fn,fno,show,nshufflei,nbins)`, you can draw a figure showing histograms, heatmaps, and scatter maps for major six parameters (array displacements, shallow gradients, gradient depth).
* fn: Input file ("sample.out" by default)
* fno: Output figure name
* show: if `show=true`, a figure is shown on REPL and is not saved as a file (`show=false` by default)
* nshuffle: number of plots for each parameter (if all samples are plotted, the figure is crowded; thus, `nshuffle` of samples are randomly picked; if `nshuffle=0`, all samples are plotted; `nshuffle=10000` by default)
* nbins: Number of histogram's intervals (`nbins=50` by default)
You can adjust fontsizes, margins of panels, and figure size by the other arguments. 

```julia
SeaGap.plot_histogram2d(fno="histogram2d.pdf")
```    

~~~
<div class="row">
  <div class="container">
    <img src="/assets/histogram2d.jpg" width="500" height="500">
    <div style="clear: both"></div>
  </div>
</div>
~~~

Diagonal panels show histograms for individual parameter.
Upper triangle panels show scatter maps for corresponding two parameters.
Lower triangle panels show heat maps for corresponding two parameters.

If you'd like to draw a heatmap for certain two parameters, you can make it by `plot_histogram2d_each(param1,param2,fn,fno,show,nbins)`.
`param1` and `param2` are the parameter names written in "initial.inp".

```julia
SeaGap.plot_histogram2d_each("UD_disp.","S-NTD_10",fno="histogram2d_UD_SNTD-10.png")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/histogram2d_UD_SNTD-10.png" width="350" height="350">
    <div style="clear: both"></div>
  </div>
</div>
~~~

### Spatial variation of travel-time residuals (shallow gardient)

Considering from the observation equation (2), the projected travel-time residuals subtracting the deep gradient and long-term NTD ($\frac{1}{M\left(\xi_{n,k}\right)} T_{n,k}^{\mathrm{obs}}-\frac{1}{M\left(\xi_{n,k}\right)} {T}^{\mathrm{cal}}\left(\mathbf{p}_k+\delta \mathbf{p}, \mathbf{u}\left(t_{n},{\bf b}_0\right), v_0\right)- \sum_{m=0}^{4} \gamma_{m}t^m_n-\frac{D}{2}\mathbf{g}_{\mathrm{s}}\mathbf{h}_{n,k}$) indicate the spatial variation due to the shallow gradients.
`plot_gradmap(xrange,yrange;autoscale,fn1,fn2,fno,show)` draws a sea-surface platform track with color of the spatial variation due to the shallow gradients.
* xrange: Range of EW component in meters when `autoscale=false`
* yrange: Range of NS component in meters when `autoscale=false`
* autoscale: If `autoscale=true`, the plot range is automatically defined
* fn1: File name of the seafloor transponder positions ("pxp-ini.xyh")  
* fn2: Travel-time residual file name ("residual\_grad.out")
* fno: Output figure name ("gradmap.pdf" by default)
* show: if `show=true`, a figure is shown on REPL and is not saved as a file (`show=false` by default)

```julia
SeaGap.plot_gradmap((-2500,2500),(-2500,2500),autoscale=false,fno="gradmap.png")
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/gradmap.png" width="400" height="400">
    <div style="clear: both"></div>
  </div>
</div>
~~~

### Temporal variation of the travel-time residuals

Time-series of the travel-time residuals are plotted by `plot_ntdgrad(ntdrange,resrange;autoscale,fn,fno,show)`.
* ntdrange: Range of the upper two panels in msec when `autoscale=false`
* resrange: Range of the lowest panels in msec when `autoscale=false`
* autoscale: If `autoscale=true`, the plot range is automatically defined
* fn: Travel-time residual file name ("residual\_grad.out")
* fno: Output figure name ("ntdgrad.pdf" by default)
* show: if `show=true`, a figure is shown on REPL and is not saved as a file (`show=false` by default)

```julia
SeaGap.plot_ntdgrad()
```

~~~
<div class="row">
  <div class="container">
    <img src="/assets/ntdgrad.png" width="600" height="600">
    <div style="clear: both"></div>
  </div>
</div>
~~~

The upper panel shows the projected travel-time residuals ($\frac{1}{M\left(\xi_{n,k}\right)} T_{n,k}^{\mathrm{obs}}-\frac{1}{M\left(\xi_{n,k}\right)} {T}^{\mathrm{cal}}\left(\mathbf{p}_k+\delta \mathbf{p}, \mathbf{u}\left(t_{n},{\bf b}_0\right), v_0\right)$) by cross symbols.
Moreover, black dots show combination of the 3d B-splined NTD and the deep gradient contribution ($\sum_{j=1}^{J}c_j\Phi_j(t_n)+\frac{D}{2}\mathbf{g}_{\mathrm{s}}\mathbf{h}_{n,k}$)

The middle panel shows the projected travel-time residuals subtracting the deep gradient contribution ($\frac{1}{M\left(\xi_{n,k}\right)} T_{n,k}^{\mathrm{obs}}-\frac{1}{M\left(\xi_{n,k}\right)} {T}^{\mathrm{cal}}\left(\mathbf{p}_k+\delta \mathbf{p}, \mathbf{u}\left(t_{n},{\bf b}_0\right), v_0\right) - \frac{D}{2}\mathbf{g}_{\mathrm{s}}\mathbf{h}_{n,k}$) by the cross symbols.
Moreover, the 3d B-splined NTD, the long-term NTD, and combination of the long-term NTD and the shallow gradients are shown by black, red, and blue curves, respecively.

The lower panel shows the travel-time residuals in the observation equation 1.

## Run MCMC processing with additional constraints

As a developing function, you can perform the MCMC processing with additional constraints.
If you have acoustic data obtained from only a fixed-point survey, it is difficult to obtain stable positioning results by `static_array_mcmcgrad()` as indicated by [Tomita & Kido (2022)](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-022-01740-0).
Here, `static_array_mcmcgradc(lat,TR_DEPTH,NPB,ss,gd0,sdg)` provides two additional constraints as prior distributions.

One prior distribution is a Cauchy distribution for shallow gradients with the location parameter of zero and the scale parameter of `ss`.
Shallow gradients can be determined by acoustic data obtained by spatially distributed sea-surface platform.
Thus, it is plausible to constrain the shallow gradients to be zero for stability when you do not have sufficient spatial distribution of a sea-surface platform.
Thus, `static_array_mcmcgradc()` provides the Cauchy distribution: if data is insufficient, the shallow gradients are constrained to be zero; if data is sufficient, the shallow gradients are followed with the data apart from zero because a Cauchy distribution allows outliers.
`ss` is set to be 3.e-4 as the default value, considering the results of [Tomita & Kido (2022)](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-022-01740-0).

The other prior distribution is a Normal distribution for the gradient depth with the mean of `gd0` and the standard deviation of `sdg`.
If the data quantity is insuffient or the data include outliers, the gradient depth tends to have quite small or large value.
To avoid this, the prior distribution can constrain the gradient depth.
Refering the results of [Tomita & Kido (2022)](https://earth-planets-space.springeropen.com/articles/10.1186/s40623-022-01740-0), `gd0=0.65` [km] and `sgd=0.1` [km] are defined as the default values.

The parameters for the prior distributions can be optionally changed as following:
```julia
ss=5,e-4; gd0=0.5; sdg=0.2
SeaGap.static_array_mcmcgradc(lat,TR_DEPTH,NPB,ss,gd0,sdg,tscale=10.0)
```

Formats of the input and output files of `static_array_mcmcgradc()` are same with those of `static_array_mcmcgrad()`.
The options to improve the acceptance ratio in `static_array_mcmcgrad()` (e.g., `lscale`, `aventd`, and `NSB`) are also used.

