High throughput calculations via few job submissions
======================================================
 
The following represents submission scripts designed for running many calculations in situations when it is either impractical or impossible to submit each calculation as an individual job to the schedular.   
 
When designing your workflow it is helpful to note that having an excessive number of directories within a single directory can be difficult to navigate compared to nesting folders within subfolders. 
 
These jobs are divided into two use cases which can hopefully be modified to suit a given workflow  

 
Running multiple of the same calculation
------------------------------------------

A classic example of this is running multiple DFT calculations to obtain training data for an ECG model.  The set up for this calculation involves setting up a nested set of folders; for this job  0/0 - 9/99 each of which contains the geometry file "MD_frame.xyz"  which contains the geometry of a single molecular configuration. Note that these files all have the same name (such that the same orca input file can be used for all calculations) and as such are distinguished by the folder name (and any comments in the file that may be written by e.g. MDAnalysis). This particular job submission script would then be submitted from the folder which contains the uppermost folder layer, 0..9.  

.. code-block:: bash

   #!/bin/bash 
   #$ -N orca_calc 
   #$ -cwd 
   #$ -o run.out 
   #$ -e run.err 
   #$ -pe orte 8
   #$ -q all.q 
  
   module load openmpi/4.1.1 
  
   for i in $(ls -vd */ | sed 's:/::') 
   do 
  
        cd "$i" || exit 
  
        for j in $(ls -vd */ | sed 's:/::') 
        do 
  
                cd "$j" || exit 
  
                if [ ! -f orca_calc.out ]; then 
                pwd 
                cp /full/path/to/input/orca_calc.inp . 
                /share/apps/orca/orca_5_0_3/orca orca_calc.inp > orca_calc.out 
                fi 
  
                cd ../ || exit 
  
        done 
        cd ../ || exit 
    done
 
Running the same calculation on multiple inputs
-------------------------------------------------

This is a nearly identical case however in this case the job submission script explicitly uses the folder names as variables used as input in the calculation. This specific example involves calculating point charges for coarse-grained configurations of the molecules in the OMG database.  The folders in the "i" loop denote the monomer ID, and the folders in the "j" loop denote the conformer number.   These values are then used as arguments in the python script "CG_dipole.py" Note that it is possible avoid using additional arguments by setting the variables to be pulled directly from the folder where the calculation is being done. Which of these cases is preferred will likely vary depending on the use case.  

.. code-block:: bash

   #!/bin/bash 
   #$ -N ua_pc 
   #$ -cwd 
   #$ -o run.out 
   #$ -e run.err 
   #$ -pe orte 1 
   #$ -q all.q 
  
   module load anaconda/3-2022.05 
   source activate kidder_zML3 
  
   for i in $(ls -vd */ | sed 's:/::') 
   do 
        cd "$i" || exit 
  
        for j in $(ls -vd */ | sed 's:/::') 
        do 
                cd "$j" || exit 
  
                if [ ! -f Summary_dipole.txt ]; then 
  
                echo "$i" "$j" 
  
                        python /home/kkidder/Property_GBCG/PC_Weights/PC_assignments/UA_ESP/CG_dipole.py "$i" "$j" 
                fi 
  
                cd ../ || exit 
  
        done 
  
        cd ../ || exit 
  
    done 
 
An alternative option involves using "sed" to replace a generic placeholder variable in the script/input file with a value determined by the folder names. For example in the script below, which was used to submit many individual jobs, the job name in the original file ``/expanse/lustre/projects/slc133/kmk619/SGCE/1UBQ/qsub.sb`` is set to NAME.  The script replaces the instance of NAME in the input file, ``/expanse/lustre/projects/slc133/kmk619/SGCE/1UBQ/$i/$j/$k/$l/qsub.sb``, with a job name that identifies the simulation based on its identifying parameters that are set by the folder name. A similar technique could be used to modify orca input files, python scripts, etc. 

.. code-block:: bash
 
   #!/bin/bash 
 
   for i in $(ls -vd */ | sed 's:/::') 
   do 
  
     cd "$i" || exit
     for j in $(ls -vd */ | sed 's:/::') 
     do  
  
        cd "$j" || exit 
        for k in $(ls -vd */ | sed 's:/::') 
        do  
  
           cd "$k" || exit 
           for l in $(ls -vd */ | sed 's:/::') 
           do 
  
             cd "$l" || exit 
             if [ ! -f MC_*.out ];then 
  
                  cp /expanse/lustre/projects/slc133/kmk619/SGCE/1UBQ/qsub.sb . 
                  var=$(echo MC_"$i"_"$k"_"$l" | cut -c 1-12) 
                  # echo $var 
                  sed -i "s:NAME:"$var":" qsub.sb  
                  # pwd 
                  sbatch qsub.sb 
              fi 
              cd ../ || exit 

           done 
           cd ../ || exit 
         
         done 
         cd ../ || exit 
  
      done  
      cd ../ || exit 
  
   done
