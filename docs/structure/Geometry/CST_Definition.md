# CST Definition

| Variable                 | Decscription| 
|-----------------------------|----------------|
|`upper`| upper surface CST coefficients (specify dvNum to define how many)|
|`lower`| lower surface CST coefficients (specify dvNum to define how many)|
|`N1`| first class shape parameter for both upper and lower surfaces (adds a single DV)|
|`N2`| second class shape parameter for both upper and lower surfaces (adds a single DV)|
|`N1_upper`| first class shape parameters for upper surface (adds a single DV)|
|`N1_lower`| first class shape parameters for lower surface (adds a single DV)|
|`N2_upper`| second class shape parameters for upper surface (adds a single DV)|
|`N2_lower`| second class shape parameters for lower surface (adds a single DV)|
|`chord`| chord length in whatever units the point set length is defined and scaled to keep the leading edge at the same position (adds a single DV)|

```python
Geometry_data = Geometry()

# Geometry to analyze
''' Could be airfoil or wing
    Airfoils can be parametrically defined using the PARSEC methods
    Wings are defined only using the existing CAD file and work either for
    straight tapered wings with or without the kink'''

Geometry_data.type = 'airfoil'

# Reference values
Geometry_data.reference_values = {
    "Area"   : 2.62,
    "Length" : 2.62,
    "Depth"  : 1,
    "Point"  : [0.25*2.62,0,0]              # reference point about which the moment is taken
}

segment = Segment()
segment.tag                = 'section_1'
segment.chord              = 2.62
segment.Airfoil.files      = {
    "upper" : "main_airfoil_upper_1.dat",
    "lower" : "main_airfoil_lower_1.dat"
}
segment.Airfoil.CST = {
                "upper" :[0.20095, 0.26864, 0.10933, 0.29307,\
                              0.12099, 0.21197, 0.18002, 0.18408],                    
                "lower" :[-0.20095, 0.05433, -0.46373, 0.25546,\
                              -0.40375, 0.01032, -0.14109, -0.11217],    
                "N1 upper" : 0.5,
                "N1 lower" : 0.5,
                "N2 upper" : 1.0,
                "N2 lower" : 1.0,
                "yte upper" : 0.001,
                "yte lower" : -0.001 
}
Geometry_data.Segments.append(segment)

segment.plot_airfoil = True

```