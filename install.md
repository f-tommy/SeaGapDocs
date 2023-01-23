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

If not, the related packages are not correctly installed.
If so, you can forcely activate SeaGap by:

```julia-repl
julia> ]
(@v1.8) pkg> activate ~/.julia/packages/SeaGap
(SeaGap) pkg> 
        [backspace]
julia> using SeaGap
```

If you cannot still use SeaGap, you should install the related packages of the third parties:
```julia-repl
julia> ]
(@v1.8) pkg> add GMT Distributions Optim PDFmerger Dierckx Plots
        [backspace]
julia> include("~/.julia/packages/SeaGap/src/SeaGap.jl")
```

This procedure provide a situation that SeaGap is imported.
 
