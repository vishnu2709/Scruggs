SGE information
=================

The page aims to provide a list of SGE keywords and commands that may be useful in the context of job submission, job/queue monitoring etc.

Job script keywords
---------------------
This includes the ones discussed in the Jobs section

.. code-block:: bash

  # This specifies the name of the job
  #$ -N lammps 

  # specify that we want to run in current directory
  #$ -cwd

  # specify the file to write stdout to 
  #$ -o tetra.out

  # specify the file to write stderr to
  #$ -e tetra.err

  # specify how many cores you want to use in your job (max 48)
  # Here ORTE is the parallel environment which manages the OpenMPI integration for SGE. I don't know more about this.
  #$ -pe orte 48

  # Specify which queue to submit your job to
  #$ -q all.q

  # Specify which node to submit to
  #$ -l hostname=compute-0-4.local

  # Specify walltime
  #$ -l h_rt=48:00:00

  # Specify priority (do not use unless instructed)
  #$ -p 20

  # Specify when to send an email, b - at the beginning, e - when it ends, a - if it aborts
  #$ -m bea

  # Which email address to send to
  #$ -M vishnura@illinois.edu

  # Merge standard out and standard err to one file
  #$ -j y

  # Request a reservation, which means that the job will only run when the number of cores you requested are available on a single node. 
  # Otherwise your job will still get the requested number of cores, but it can be distributed across nodes.
  #$ -R y
