Software
===========

Using existing software
-------------------------

Scruggs contains various scientific codes and libraries that may be useful to you. Here is the current list, which can be accessed using ``module avail``.

.. code-block:: console

   anaconda/3-2020.11   gaussian/g09         gsl/2.6              lammps/29Oct20       openbabel/3.1.1      openmpi/3.1.4-intel  orca/5.0.3
   anaconda/3-2022.05   gaussian/g16         intel/oneAPI         lammps/29Oct20-intel openmpi/2.1.6        openmpi/4.1.1        orca/6.0.0
   cmake/3.19           gcc/10.3             lammps/23Jun22-intel nwchem/7.0           openmpi/3.1.4        openmpi/4.1.6

If you desire to access any of these, use the ``module load`` command, for example

.. code-block:: console

  [vishnura😀10:14 AMscruggs:~]$ module load intel
  [vishnura😀10:14 AMscruggs:~]$ module list
  Currently Loaded Modulefiles:
     1) rocks-openmpi   2) intel/oneAPI

To remove a module from your environment, use the ``module unload`` command

Compiling your software
-------------------------

If you need a library/software that is not available in the module, you are free to compile it for yourself. Scruggs has gcc and intel compilers, and the openmpi library for parallel applications. It also has `cmake` (PS: In my opinion, the cmake version on Scruggs is a bit outdated. I recommend getting a newer cmake version from online and buiding it).

For Python based software, you can load the anaconda module.

For GPU accelerated software, it is important for you to build your GPU code on the compute-0-6 and compute-0-7 GPU nodes directly. To do this, you can ssh to the nodes directly.

.. code-block:: console

   [vishnura😀10:15 AMscruggs:~]$ ssh compute-0-6
   Last login: Mon Apr 14 09:39:56 2025 from scruggs.local
   Rocks Compute Node
   Rocks 7.0 (Manzanita)
   Profile built 19:35 13-Oct-2021

   Kickstarted 19:41 13-Oct-2021 
   [vishnura😀10:16 AMcompute-0-6:~]$ nvidia-smi
   Mon Apr 14 10:24:36 2025       
   +-----------------------------------------------------------------------------+
   | NVIDIA-SMI 470.57.02    Driver Version: 470.57.02    CUDA Version: 11.4     |
   | -------------------------------+----------------------+----------------------+
   | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
   |                               |                      |               MIG M. |
   |===============================+======================+======================|
   |   0  NVIDIA A30          Off  | 00000000:01:00.0 Off |                    0 |
   | N/A   30C    P0    32W / 165W |      0MiB / 24258MiB |      0%      Default |
   |                               |                      |             Disabled |
   +-------------------------------+----------------------+----------------------+
   |   1  NVIDIA A30          Off  | 00000000:41:00.0 Off |                    0 |
   | N/A   28C    P0    28W / 165W |      0MiB / 24258MiB |      0%      Default |
   |                               |                      |             Disabled |
   +-------------------------------+----------------------+----------------------+
   |   2  NVIDIA A30          Off  | 00000000:81:00.0 Off |                    0 |
   | N/A   29C    P0    32W / 165W |      0MiB / 24258MiB |      0%      Default |
   |                               |                      |             Disabled |
   +-------------------------------+----------------------+----------------------+
   |   3  NVIDIA A30          Off  | 00000000:C1:00.0 Off |                    0 |
   | N/A   29C    P0    31W / 165W |      0MiB / 24258MiB |     16%      Default |
   |                               |                      |             Disabled |
   +-------------------------------+----------------------+----------------------+
                                                                               
   +-----------------------------------------------------------------------------+
   | Processes:                                                                  |
   |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
   |        ID   ID                                                   Usage      |
   |=============================================================================|
   |  No running processes found                                                 |
   +-----------------------------------------------------------------------------+

Here the CUDA libraries are present in the deafult location (/usr/local/cuda), so "most" softwares would manage to locate it by itself. If it doesn't, reach out for help at vishnura@illinois.edu 
