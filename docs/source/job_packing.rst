Packing Runs 
===========================

You might find yourself in a situation where you need to run the same code in multiple folders, with each folder containing different input files/input parameters etc. In this case, you need to submit a job for each folder, you can pack these runs within a smaller number of jobs. Let's explore this idea with different examples

Serial Python runs
--------------------
In this example, let's runs to be single core python runs in the different folders. Let the folders be numbered ``f1,f2...f48``, and we want to run a simple python script in each folder that takes the number, squares it, and then runs a nested loop and prints out the numbers.

.. code-block:: python

   import sys
   import time

   input_number=float(sys.argv[1])

   sq = input_number**2

   f = open("out.dat", "w")
   f.write(str(sq)+"\n")
   f.close()

   for i in range(1000):
     for j in range(1000):
        print(input_number,i,j)

The second part is a bit random, but it exists so that we can see that the different runs are happening parallely.

This can be run using the following script

.. code-block:: bash

   #!/bin/tcsh
   #$ -N pyjob
   #$ -cwd
   #$ -o pyjob.out
   #$ -e pyjob.err
   #$ -pe orte 48
   #$ -q all.q

   for s in {1..48}
   do
   cd f$s
   python /home/vishnura/scruggs_scripts/simple_python.py $s &
   cd ../
   done
   wait

The ``&`` indicates a background job, without this, the script would not move forward until the python code fully executes. It is also important to end the script with ``wait`` when using background jobs, as otherwise the script will terminate and the background jobs will be killed.

When this job is run, you will find that there is an out.dat file in each folder, containing the squared number

.. code-block:: console

   [vishnura😀11:49 AMscruggs:~/scruggs_scripts]$ cd f41/
   [vishnura😀11:52 AMscruggs:~/scruggs_scripts/f41]$ cat out.dat 
   1681.0

Additionally, all our looped print statement would have ended up in the ``pyjob.out`` file. Since all the python runs were parallel, all of them would have outputted to stdout simultaneously, leading to a disordered mess there

.. code-block:: python

  (18.0, 0, 547)
  (18.0, 0, 548)
  (18.0, 0, 549)
  (18.0, 0, 550)
  (18.0, 0, 551)
  (18.0, 0, 552)
  (18.0, (25.0, 0, 0)
  (25.0, 0, 1)
  (25.0, 0, 2)
  (25.0, 0, 3)
  (25.0, 0, 4)
  (25.0, 0, 5)
  (25.0, 0, 6)

Replace simple_python.py with your python script. Since each node has 48 cores, the maximum number of runs you can fit into one job is 48. Note that these 48 runs will share the memory of 1 node. You need to ensure that 48x the memory usage of your script will be less than memory of a node, otherwise your run might crash. In that case, lower the number of runs per job.

LAMMPS
-------

For parallel LAMMPS runs, you can try this really straightforward strategy

.. code-block:: bash
   
   #!/bin/tcsh
   #$ -N bammps
   #$ -cwd
   #$ -o tetra.out
   #$ -e tetra.err
   #$ -pe orte 48
   #$ -q all.q
   
   module load intel
   
   cd m30/run4/
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../m20/run4/
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../m15/run4/
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../m10/run4
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &

   cd ../../m5/run4
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../m0/run4
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../5/run4/
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../10/run4/
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../15/run4/
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../20/run4/
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../30/run4/
   mpirun -np 4 /home/vishnura/lammps/build/lmp -i MD_Simulation.in -screen stdout &
   
   cd ../../
   wait

Here I have a bunch of different folders (``30,20,15,10,5,m0,m5,m10,m15,m20,m30``), and I'm running LAMMPS with 4 cores in each folder. This works if it's good enough to use 4 cores to each individual run. Otherwise you use more cores per folder and less folders per job. Again, the same memory issues from the previous section apply here.

ORCA
-------
For ORCA, you don't need to specify the number of cores in the job script, but in the input file. Let's take the example of F4TCNQ, a popular electron acceptor molecule. I want to do ORCA runs for the neutral molecule and the molecule with one electron extra. I can make two folders, ``neutral`` and ``charged``, and in each input file I specify 24 cores.

.. code-block:: bash
   
   # Neutral
   ! UKS wB97X-D3 SP def2-SVP  def2/J RIJCOSX NormalPrint PrintBasis PrintMOs
   %output
   Print [P_Overlap] 1
   end
   %pal
   nprocs 24
   end
   
   * xyz 0 1
  N    28.161     29.755    20.987
  C    28.321     30.899    20.940
  C    28.522     32.360    20.952
  C    29.066     32.665    22.200
  N    29.550     32.918    23.230
  C    28.185     33.214    19.921
  C    27.678     32.675    18.669
  F    27.652     31.385    18.353
  C    27.279     33.542    17.711
  F    26.713     32.945    16.653
  C    27.379     34.939    17.789
  C    27.027     35.679    16.608
  C    27.945     35.498    18.986
  F    28.030     36.797    19.188
  C    28.257     34.717    19.999
  F    28.734     35.289    21.103
  C    26.545     35.147    15.338
  N    26.111     34.734    14.372
  C    27.303     37.086    16.359
  N    27.486     38.217    16.130
   *
   
.. code-block:: bash
   
   # Charged
   ! UKS wB97X-D3 SP def2-SVP  def2/J RIJCOSX NormalPrint PrintBasis PrintMOs
   %output
   Print [P_Overlap] 1
   end
   %pal
   nprocs 24
   end
   
   * xyz -1 2
  N    28.161     29.755    20.987
  C    28.321     30.899    20.940
  C    28.522     32.360    20.952
  C    29.066     32.665    22.200
  N    29.550     32.918    23.230
  C    28.185     33.214    19.921
  C    27.678     32.675    18.669
  F    27.652     31.385    18.353
  C    27.279     33.542    17.711
  F    26.713     32.945    16.653
  C    27.379     34.939    17.789
  C    27.027     35.679    16.608
  C    27.945     35.498    18.986
  F    28.030     36.797    19.188
  C    28.257     34.717    19.999
  F    28.734     35.289    21.103
  C    26.545     35.147    15.338
  N    26.111     34.734    14.372
  C    27.303     37.086    16.359
  N    27.486     38.217    16.130
   *

The script would be similar to the previous cases

.. code-block:: bash
   
   #!/bin/tcsh
   #$ -N ORCA
   #$ -cwd
   #$ -o tetra.out
   #$ -e tetra.err
   #$ -pe orte 48
   #$ -q all.q
   
   module load openmpi/4.1.1
   module load orca/5.0.3
   module load anaconda/3-2020.11
   
   cd neutral/
   /share/apps/orca/orca_5_0_3/orca tetra.inp > tetra.log &
   cd ../charged/
   /share/apps/orca/orca_5_0_3/orca tetra.inp > tetra.log &
   cd ../
   wait

These are the three main tools that people use in this group, hence they have been picked as examples. If you need help running something else, please reach out (vishnura@illinois.edu)
