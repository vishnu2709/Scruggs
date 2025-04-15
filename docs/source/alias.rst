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
