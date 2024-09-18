# run_aerodynamic_analysis(Input) 

**Steps:**

1. Unpack all inputs

2. Run the airfoil generation script in a separate directory (using pygeo if 3d case, else parsec or cst)

3. Generate Mesh or use existing mesh

4. Solve using SU2


## run_SU2.py

### solve()
**Steps:**

1. For each combination of altitudes, angles of attack and mach in freestream setting, firstly check the warmstart condition and retrieve filename using run_SU2_config function

2. Create directory of the results

3. Run the solver parallelly using MPI, results will be written in SU2_output.log

4. Read results and store it in excel

```python
import os
import shutil
import subprocess
import numpy as np
from Methods.Mesh.mesh_pre_process_2D     import mesh_pre_process_2D
from Methods.Mesh.mesh_pre_process_3D     import WingMeshPreProcess 
from Methods.Solver                       import run_SU2


def run_aerodynamic_analysis(Input):

    '''Main function to run the analysis
    
        Inputs:
            Input.Solver     - Solver settings
                  Geometry   - Geometric settings
                  Freestream - Freestream conditions
                  Mesh       - Mesh settings


        Outputs:
           

        Assumptions:

    '''


    # Unpack all inputs
    Solver     = Input.Solver
    Geometry   = Input.Geometry
    Freestream = Input.Freestream
    Mesh       = Input.Mesh
 
    # Run the airfoil/wing generation script in a separate directory
    if Geometry.generate is True:
        file_path = os.path.join(Solver.working_dir, 'Geometry_files/')
        if os.path.exists(file_path):
            shutil.rmtree(file_path)
        os.mkdir(file_path)
        os.chdir(file_path)
        num_segm = len(Geometry.Segments.keys())
        for i in range(num_segm):
            if len(Geometry.Segments[i].Airfoil.PARSEC) != 0:
                Geometry.Segments[i].create_PARSEC_airfoil()
            elif len(Geometry.Segments[i].Airfoil.CST) != 0:
                Geometry.Segments[i].create_CST_airfoil()


        # Create a wing using pygeo if a 3D case is defined 
        if Solver.dimensions == '3d':  
            Geometry.create_wing_geometry()

         
    # Mesh the geometry
    if Mesh.meshing is True:

        # Calculate mesh step size based on Y+
        Mesh.calculate_initstepsize(max(Freestream.Mach), min(Freestream.Altitude), Geometry.reference_values["Length"], Mesh.Yplus)

        # Assign a flag for an inviscid solver
        if Solver.turbulence_model == 'Inviscid':
            Inviscid_flag = True
        else:
            Inviscid_flag = False

        # Update the Glyph script depending on the geometry 
        if  Solver.dimensions == '2d':
            mesh_pre_process_2D(Solver.working_dir,Geometry,Mesh)
            if  Geometry.Segments[0].TrailingEdgeDevice.type == 'Slotted': 
                # Update the Glyph script - Slotted flap airfoil                               
                from Methods.Mesh.glyph_updater_flapped   import update_glyph_script_fl    
                update_glyph_script_fl(Mesh,Solver.working_dir) 
            else:
                # Update the Glyph script - clean Airfoil or one with a plain flap 
                from Methods.Mesh.glyph_updater_clean     import update_glyph_script_cl
                update_glyph_script_cl(Mesh,Solver.working_dir)

        elif Solver.dimensions == '3d':
            wing_meshing = WingMeshPreProcess()
            wing_meshing.write_glyph_file(Solver.working_dir,Geometry,Mesh,Solver,Inviscid_flag)
        else:
            print("Specify 2d or 3d solver dimensions")
        

        # Run Pointwise glyph script to generate the mesh
        os.chdir(Mesh.tclsh_directory)
        if Mesh.operating_system == "WINDOWS":
            working_dir_change = Solver.working_dir.replace('/','\\')
            full_glyph_path    = working_dir_change + "\\" + Mesh.glyph_file 
            p = subprocess.call(['tclsh ',full_glyph_path], stderr= None, stdin=subprocess.PIPE)    
        else:
            full_glyph_path = Solver.working_dir + "/" + Mesh.glyph_file 
            p = subprocess.call('./pointwise ' + '-b ' + full_glyph_path, shell = True, stdin=subprocess.PIPE)


    # Run CFD solution
    run_SU2.solve(Solver,Freestream,Mesh,Geometry)
    

    print("Analysis completed")


    return
```

