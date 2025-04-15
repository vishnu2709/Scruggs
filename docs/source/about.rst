About
=====

Scruggs contains 16 CPU nodes, each consisting of a dual socket Intel Xeon Gold CPU with 48 cores. It also contains 2 GPU nodes, each with a 24-core AMD EPYC 7352 processor and 4 NVIDIA A30 GPUs, each with 24 GB virtual memory.

How to login
----------------

When on campus network, you can connect to Scruggs using SSH using ``yourid@scruggs.scs.illinois.edu``, for example

.. code-block:: console

   (base) vishnura@wirelessprv-10-195-28-85 ~ % ssh vishnura@scruggs.scs.illinois.edu

.. note::
   When setting up your local machine, we recommend using your Illinois NetID as your username. This helps avoid issues when connecting to the cluster, since your local and remote usernames will match. With this setup, you can simply connect to Scruggs using:

   .. code-block:: console

      (base) yourid@local-pc ~ % ssh scruggs.scs.illinois.edu

   This is equivalent to ``ssh yourid@scruggs.scs.illinois.edu``, and it helps streamline your workflow.

If you do not have an account on Scruggs or you are unable to log in, reach out to Prof. Nick Jackson (jacksonn@illinois.edu).

Note that Scruggs is not accessible outside the campus network. To access Scruggs from outside you need to use a VPN. Instructions on how to download and set up the VPN to connect to campus is given at https://answers.uillinois.edu/illinois/98773
