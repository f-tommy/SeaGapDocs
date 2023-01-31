+++
title = "Installation"
hascode = true
date = Date(2023, 1, 22)
rss = "A short description of the page which would serve as **blurb** in a `RSS` feed; you can use basic markdown here but the whole description string must be a single line (not a multiline string). Like this one for instance. Keep in mind that styling is minimal in RSS so for instance don't expect maths or fancy styling to work; images should be ok though: ![](https://upload.wikimedia.org/wikipedia/en/3/32/Rick_and_Morty_opening_credits.jpeg)"

tags = ["syntax", "code"]
+++


# How to install SeaGap

First, you have to install Julia in your PC: [https://julialang.org/](https://julialang.org/).
The SeaGap can be used by Julia v > 1.7.

The package of SeaGap is stored on Github: [https://github.com/f-tommy/SeaGap](https://github.com/f-tommy/SeaGap).
You can install the following procedure after running Julia (`$ julia`):

```julia-repl
julia> ]
(@v1.8) pkg> add https://github.com/f-tommy/SeaGap
        [backspace]
```

"]" turn on the package mode, and you can install Julia packages.
After this procedure, the package of SeaGap is downloaded on your PC (generally,  ~/.julia/packages/).

If SeaGap is correctly installed, you can use SeaGap by:

```julia-repl
julia> using SeaGap
```

Some troubles in installing [GMT.jl](https://github.com/GenericMappingTools/GMT.jl) have been reported because of some problems in GMT itself.
If so, you should conduct the following command before the installation.

```julia-repl
julia> ENV["FORCE_INSTALL_GMT"] = true
```

If you cannot solve the issue on GMT, a restricted version of SeaGap, named [SeaGapR](https://github.com/f-tommy/SeaGapR) is available. SeaGapR can conduct most of functions in SeaGap except `ll2xy.jl`.

```julia-repl
julia> ]
(@v1.8) pkg> add https://github.com/f-tommy/SeaGapR
        [backspace]
julia> using SeaGapR
```

If you cannot still use SeaGap, you should install the related packages of the third parties:
```julia-repl
julia> ]
(@v1.8) pkg> add GMT Distributions Optim PDFmerger Dierckx Plots
        [backspace]
julia> include("~/.julia/packages/SeaGapR/(directry name depending on your PC)/src/SeaGapR.jl")
```

This procedure provide a situation that SeaGap is imported.
 
