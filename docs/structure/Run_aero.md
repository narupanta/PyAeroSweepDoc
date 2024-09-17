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

