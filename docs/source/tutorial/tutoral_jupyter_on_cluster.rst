Running Jupyter lab on RC cluster 
=================================
All credit to  **Nils Wendt** for the method.

Part 1 
---------------------------------
#. Login to your RC cluster

   .. code-block:: none

        ssh <username>@login.rc.fas.harvard.edu
    
#. Launch an interactive job from the cluster with around 8 GO of RAM and a specified time and leave this terminal a side

   .. code-block:: none

    srun --pty -p cox -t 0-12:00 --mem 8000 /bin/bash

#. Forward a port from your local computer to the cluster, this port will be used for Jupyter Lab
    
   .. code-block:: none

    ssh -L <port number>:localhost:<port number> <username>@login.rc.fas.harvard.edu


#. In the same termiinal, you should be now on the cluster. Forward a port from the cluster to the interactive job allocated partition. Partition name is obtained from the interactive job terminal, for example ``coxgpu01``. The port number needs to be the **same**, for example ``2626``

   .. code-block:: none

     ssh -L <port number>:localhost:<port number> <partition>

You are all set and you only need to launch Jupyter Lab with the port number specified previously ! We will continue working from the last terminal which is now running on the partition specified earlier.

Part 2
---------------------------------
#.  Make sure to load anaconda module on your RC

    .. code-block:: none

      module load Anaconda3/5.0.1-fasrc02

#. Create/Activate your conda environment you like to use

   .. code-block:: none

     If not already created : conda create -n <environment>
 
     source activate <environment>
#. Launch Jupyter Lab 

   .. code-block:: none

     jupyter lab --no-browser --port=<port number>
#. Finally copy the Jupyter Lab link you get on the terminal and paste on your local computer browser. You are all set now.

Useful aliases and bash functions
---------------------------------
#. Local computer 

   .. code-block:: none

    fwport() { ssh -L ${1}:localhost:${1} <username>@login.rc.fas.harvard.edu; }

#. RC cluster

   .. code-block:: none

    alias loadconda='module load Anaconda3/5.0.1-fasrc02'


    gpujob() { srun --pty -p cox -t 0-12:00 --mem ${1} /bin/bash; }

    
    fwport() { ssh -L ${1}:localhost:${1} ${2}; }

    
    fwjl() { jupyter lab --no-browser --port=$1; }

 
