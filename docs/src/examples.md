!!! note

    Consider installing the [GeoStatsImages.jl](https://github.com/juliohm/GeoStatsImages.jl) package.

## Unconditional

An example of unconditional simulation (i.e. simulation without data).
This is the original Efros-Freeman algorithm for texture synthesis with a
few additional options.

```julia
using ImageQuilting
using GeoStatsImages

TI = training_image("Strebelle")
reals = iqsim(TI, 62, 62, 1, size(TI)..., nreal=3)

TI = training_image("StoneWall")
reals, cuts, voxs = iqsim(TI, 13, 13, 1, size(TI)..., nreal=3, debug=true)
```
![Unconditional simulation](images/unconditional.png)

## Hard data

Hard data (i.e. point data) can be honored during simulation. This can be useful
when images represent a spatial property of a physical domain. For example, the
subsurface of the Earth is only known with certainty at a few well locations.

```julia
using ImageQuilting
using GeoStatsImages

TI = training_image("Strebelle")

data = HardData()
push!(data, (50,50,1)=>1)
push!(data, (190,50,1)=>0)
push!(data, (150,170,1)=>1)
push!(data, (150,190,1)=>1)

reals, cuts, voxs = iqsim(TI, 30, 30, 1, size(TI)..., hard=data, debug=true)
```
![Hard data conditioning](images/hard.gif)

![Hard data conditioning](images/hard.png)

## Soft data

Sometimes it is also useful to incorporate auxiliary variables defined in the
domain, which can guide the selection of patterns in the training image. This
example shows how to achieve this texture transfer efficiently.

```julia
using ImageQuilting
using GeoStatsImages
using Images

TI = training_image("WalkerLake")
truth = training_image("WalkerLakeTruth")

G(m) = imfilter(m, KernelFactors.IIRGaussian([10,10,0]))

data = SoftData(G(truth), G)

reals = iqsim(TI, 27, 27, 1, size(truth)..., soft=data, nreal=3)
```
![Soft data conditioning](images/soft.png)
