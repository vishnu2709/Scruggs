Aliases and Shortcuts
=====================

Aliases
-------------------

Typing long or repetitive commands can get tedious. If you find yourself running the same command often, it's a good idea to create an *alias* — a shortcut that maps a short name to a longer command.

To define an alias, you simply choose a name and assign it to the full command. You can define this in your ``.bashrc`` or ``.zshrc`` file. A few examples that are useful in the context of our lab are given below:

.. code-block:: bash

   alias qall='qstat -f -u "*"'
   alias qrun='qstat -f -s r -u "*"'

.. note::
   In the case of using this in scruggs, you will need to do ``source ~/.bashrc`` or ``source ~/.zshrc`` to load the aliases.

On your local machine, you can define a few helpful aliases to save time. Add the following lines to your shell configuration file (e.g., ``.bashrc`` or ``.zshrc``):

.. code-block:: bash

   alias vmd=/usr/local/bin/vmd
   alias lop='ssh lop.scs.illinois.edu'
   alias scruggs='ssh scruggs.scs.illinois.edu'

Authentication Shortcuts
-------------------------

To avoid entering your password every time you connect to a server, you can set up RSA key-based authentication. This allows for a smoother workflow and enables the SSH aliases above to work without prompting for credentials.

You can even create separate SSH keys for each server, like ``lop`` and ``scruggs``. Here's how to do it:

.. code-block:: bash

   ssh-keygen
   # When prompted, enter a custom file name for the key, e.g., /home/yourname/.ssh/id_lop
   ssh-copy-id -i ~/.ssh/id_lop.pub yourid@lop.scs.illinois.edu

Repeat the process for ``scruggs`` with a different key name if desired. After this setup, your SSH connections will no longer require a password.


Mounting and Unmounting the Cluster
-----------------------------------

You can mount the cluster’s storage on your local machine, which makes it accessible like an external drive. This is especially useful for file transfers or for postprocessing data without needing to download everything.

First, make sure the ``sshfs`` package is installed on your local machine. Then, create a directory where the cluster’s storage will be mounted, and use the following command:

.. code-block:: bash

   mkdir ~/scruggs/
   sshfs yourid@scruggs.scs.illinois.edu:/home/yourid/ ~/scruggs/

Now you can browse, open, and edit files on the cluster directly from your local machine.

To unmount the cluster when you're done:

.. code-block:: bash

   fusermount -u ~/scruggs/

.. note::
   If you're on macOS, use ``umount ~/scruggs/`` instead of ``fusermount``.

.. note::
    You can add the ``sshfs`` command to your ``.bashrc`` or ``.zshrc`` as an alias to make it easier to use.

Scripts
-------

This script helps you check the status of jobs across all queues **without showing duplicate nodes**, making it easier to get a quick overview of resource usage. Save the script below to your home directory as ``check_gse.sh`` and make it executable:

.. code-block:: bash

   chmod +x check_gse.sh

You can then run it with:

.. code-block:: bash

   ./check_gse.sh

This will display all running jobs grouped by node, followed by a list of available nodes and a queue-to-node mapping.

This is a mockup of the output:

.. code-block:: console

      SGE Jobs Grouped by Node
      =========================
      NODE                 QUEUE      JOB_ID     USER       JOB_NAME             RESV/U/T    START_TIME           STATE
      ---------------------------------------------------------------------------------------------------------------
   compute-0-0.local    all.q      90001      alice      sim-alpha            0/8/48      04/10/2025 09:20:00  r    
   compute-0-0.local    all.q      90002      bob        sim-beta             0/12/48     04/10/2025 09:21:10  r    
   compute-0-1.local    all.q      90003      carol      sim-gamma            0/6/48      04/10/2025 09:23:45  r    
   compute-0-2.local    all.q      90004      dave       sim-delta            0/4/48      04/10/2025 09:25:00  r    
   compute-0-2.local    high.q     90005      eve        test-epsilon         0/0/48      04/10/2025 09:26:30  qw   

.. code-block:: bash

   #!/bin/bash

   echo "=== SGE Jobs Grouped by Node ==="
   # Print header
   header=$(printf "%-20s %-10s %-10s %-10s %-20s %-12s %-20s %-5s\n" \
   "NODE" "QUEUE" "JOB_ID" "USER" "JOB_NAME" "RESV/U/T" "START_TIME" "STATE")
   echo "$header"
   echo "---------------------------------------------------------------------------------------------------------------"

   # Collect job info and sort by node name
   qstat -f -u "*" | awk '
   BEGIN { OFS="\t" }
   /^[a-zA-Z]/ {
      split($1, queue_parts, "@")
      queue = queue_parts[1]
      node = queue_parts[2]
      resv_used_tot = $3
      next
   }
   /^[[:space:]]+[0-9]/ {
      job_id = $1
      job_name = $3
      user = $4
      state = $5
      start_time = $6 " " $7
      print node, queue, job_id, user, job_name, resv_used_tot, start_time, state
   }
   ' | sort -k1,1 | awk -F'\t' -v fmt="%-20s %-10s %-10s %-10s %-20s %-12s %-20s %-5s\n" \
   '{ printf fmt, $1, $2, $3, $4, $5, $6, $7, $8 }'

   if [ $? -ne 0 ] || [ -z "$(qstat -f -u "*")" ]; then
      echo "No jobs currently running in the cluster."
      echo ""
      echo "=== Available Nodes ==="
      qhost | grep -v "global" | grep -v "HOSTNAME"
   fi

   echo ""
   echo "=== Queue to Node Mapping ==="
   for queue in $(qconf -sql); do
      echo -n "Queue $queue: "
      qconf -sq $queue | awk '/hostlist/ {for (i=2; i<=NF; i++) printf "%s ", $i}'
      echo ""
   done
