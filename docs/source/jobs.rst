Jobs
=======

Job Submission Basics
---------------------

In most computing clusters, users run their code by submitting a *job script* — a file that tells the system what to run, where to run it, and what resources are needed. This script acts like a formal request: you specify the command to execute, the number of CPUs or GPUs, how much time you expect the job to take, and any other requirements.

Once submitted, your job enters a queue alongside others. A scheduler then decides when and where each job runs, based on available resources and job priorities.


Submitting a job
------------------
Scruggs uses the Sun Grid Engine (SGE) as the job scheduler. SGE requires the job scripts to be in a specific format. Here's a example job script

.. code-block:: tcsh

  # To use the tcsh interpreter
  #!/bin/tcsh

  # This specifies the name of the job
  #$ -N lammps 

  # specify that we want to run in current directory
  #$ -cwd

  # specify the file to write stdout to 
  #$ -o tetra.out

  # specify the file to write stderr to
  #$ -e tetra.err

  # specify how many cores you want to use in your job
  # There are 48 cores in a node, if you ask for more than 48 cores, it will split your job across multiple nodes.
  # Here ORTE is the parallel environment which manages the OpenMPI integration for SGE. I don't know more about this.
  #$ -pe orte 48

  # Specify which queue to submit your job to
  #$ -q all.q

  # Loading the modules I need
  module load intel

  # Here is the actual code that I want to run
  mpirun -np 48 /home/vishnura/lammps_mc/build/lmp -in MD_Simulation.in -screen stdout

Other SGE keywords are present in the SGE information section. Let's say this is put in a file called lammps_job_mpi.sh. We can submit the job using the command

.. code-block:: console

  [vishnura😀10:55 AMscruggs:~/ReactiveMC/actual/20/rmc/epsilon1/0]$ qsub lammps_job_mpi.sh 
  Your job 88754 ("lammps") has been submitted
  [vishnura😀10:56 AMscruggs:~/ReactiveMC/actual/20/rmc/epsilon1/0]$ 

You can see all the current jobs in the queue using the ``qstat`` command, inclusing the one we just submitted.

.. code-block:: console

    [vishnura😀10:56 AMscruggs:~/ReactiveMC/actual/20/rmc/epsilon1/0]$ qstat
    job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID 
    -----------------------------------------------------------------------------------------------------------------
    87932 0.60500 lammps     respera2     r     03/27/2025 13:22:24 all.q@compute-0-5.local           48        
    88463 0.60500 W40_GB03_p huihang2     r     04/08/2025 14:04:08 high.q@compute-1-1.local          48        
    88476 0.50713 GroupVAE   sk77         r     04/08/2025 18:16:39 gpu@compute-0-7.local              2        
    88634 0.60500 W50_GB03_p huihang2     r     04/11/2025 13:01:44 high.q@compute-1-2.local          48        
    88646 0.60500 W30_GB03_p huihang2     r     04/11/2025 16:44:44 high.q@compute-0-2.local          48        
    88721 0.50500 mo         huihang2     r     04/12/2025 16:15:30 all.q@compute-0-0.local            1        
    88751 0.51989 spc_gausjo archana3     r     04/14/2025 10:47:15 all.q@compute-0-10.local           8        
    88752 0.51989 spc_gausjo archana3     r     04/14/2025 10:47:15 all.q@compute-0-10.local           8        
    88754 0.60500 lammps     vishnura     r     04/14/2025 10:56:15 all.q@compute-1-4.local           48        
    [vishnura😀10:57 AMscruggs:~/ReactiveMC/actual/20/rmc/epsilon1/0]$

The state ``r`` means that the job is running. If the status is ``qw``, that means the job is waiting to run. If you cannot see your job in the queue, it means that it has finished running (successfully or otherwise). 

Queues
--------

Most clusters have multiple queues and Scruggs is no exception. We have three queues 

1. ``all.q`` : This is the general queue for the CPU nodes. All users are free to submit to this queue at any time. 
2. ``high.q`` : This is the high priority queue for the CPU nodes. Jobs in this queue will have a higher prority in comparison to jobs in ``all.q``. Only users with permission may run in this queue. For more information, refer to the high prority queue policy.
3. ``gpu`` : This is the queue for the GPU nodes.

For all queues, the maximum runtime for any job is 48 hours. Users may not have more than 100 jobs in the queue at any point in time.

Interactive Jobs
------------------
You can also request an interactive job by using the qlogin command

.. code-block:: console

   [vishnura😀11:01 AMscruggs:~/ReactiveMC/actual/20/rmc/epsilon1/0]$ qlogin -q all.q
   Your job 88757 ("QLOGIN") has been submitted
   waiting for interactive job to be scheduled ...
   Your interactive job 88757 has been successfully scheduled.
   Establishing builtin session to host compute-0-8.local ...
   [vishnura😀11:01 AMcompute-0-8:~]$

An interactive job gives you direct access to the compute node. This is great for debugging and quick test runs.




