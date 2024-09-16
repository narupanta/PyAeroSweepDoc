# Others

## Turbulence Model

The information of turbulence models used in this project can be found under these 2 links

* [SST Model](https://turbmodels.larc.nasa.gov/sst.html)
* [SA Model](https://turbmodels.larc.nasa.gov/spalart.html)
## Wall functions
Available for RANS, INC_RANS.

The wall function model of Nichols and Nelson (2004) has been implemented in the compressible and the incompressible solver, for the SA as well as the SST models. For the compressible solver, the wall function model takes into account the frictional heating of the wall according to the Crocco-Busemann relation when the wall boundary conditions is not isothermal. When the wall model is active, the value of the dimensional distance of the first node from the wall can be \(y^+ > 5\)
. When the wall model is not active, \(y^+ < 5\) and in addition a fine mesh is necessary close to the wall to resolve the near wall boundary layer.