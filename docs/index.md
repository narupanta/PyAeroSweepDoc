# Welcome to PyAeroSweep

## About
PyAeroSweep is a multi-functional tool that performs aerodynamic sweeps of airfoils and wings using high-fidelity CFD methods.

PyAeroSweep can perform a range of pre-processing and simulation tasks:

1. Generation of airfoil and wing geometries of various kinds:
    * **Airfoils**
        - Airfoils can be generated using PARSEC of CST parametrization methods
        - A droop nose, plain, or slotted flaps can be defined for given airfoils
        - Airfoil coordinates are imported in typical text formats
    * **Wings**
        - Generation of piesewise wing planforms using the Pygeo framework
        - Wing planforms are exported in the .igs format
        - Only clean planform capabilities are available
2. Automatic geometry meshing using Pointwise:
    * Meshing is performed using pre-defined templates that can be parameterically tuned for a particular problem
    * Viscous layers are added for unstructured meshes using TREX
    * **Airfoils**
        * Structured and unstructured mesh options available
        * Meshing of externally imported airfils is possible
    * **Wings**
        * Meshes the wing surface using the Pointwise automatic mesh generation method
3. CFD solution using SU2:
    * At the moment, can only run RANS solutions. Euler solutions are planned
    * 3D solutions for wings are currently available only with a symmetry plane
    * angle-of-attack, altitude, and Mach number sweeps are possible
    * The tool can run a similar mesh created externally
