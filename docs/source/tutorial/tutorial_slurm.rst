RC Tutorial
==========================

This tutorial will walk you through the basic steps of setting up an environment on Harvard RC server and run the Neuron Segmentation Task on the SNEMI dataset.

Prerequisites.
---------------------

#. `User ID for Harvard RC <https://www.rc.fas.harvard.edu/resources/access-and-login/>`_ 


Setup Instructions.
---------------------

#. Login into RC 

            .. code-block:: none
            
            ssh {username}@login.rc.fas.harvard.edu


#. Install Anaconda 

    #. `Tutorial <https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart>`_
    #. Follow Steps 1 - 8

#. Load modules
        .. code-block:: none

        module load centos6/0.0.1-fasrc01
        module load gcc/7.1.0-fasrc01

#. Create a new environment (with Python 3.6)
	    .. code-block:: none

		conda create -n {env_name} python=3.6

#. Activate the environment and install `PyTorch <https://pytorch.org/>`_
	    .. code-block:: none

		conda activate {env_name}
		conda install pytorch=1.0 torchvision cudatoolkit=9.0 -c pytorch

#. [Optional] Install `PyTorch Connectomics <https://zudi-lin.github.io/pytorch_connectomics/build/html/notes/installation.html>`_
	    .. code-block:: none

		git clone git@github.com:zudi-lin/pytorch_connectomics.git
		cd pytorch_connectomics
		pip install -r requirements.txt
		pip install --editable .

    Check `TROUBLESHOOTING.md <https://github.com/zudi-lin/pytorch_connectomics/blob/master/TROUBLESHOOTING.md>` in case of compilation errors._


Running Jobs (SLURM)
---------------------

There are two ways to run a job on Harvard RC: interactively (using the ``srun`` command) or by submitting a job to a queue (using the ``sbatch`` command). (A detailed documentation for the RC can be found `here <https://www.rc.fas.harvard.edu/resources/running-jobs/>`_) 

#. Login into the RC server

    #. Using srun
     
        #. ``srun`` gets an interactive bash from a machine with the requested resources

	    .. code-block:: none

		srun --pty -p ${1} -t ${2} --mem ${3} -n ${4} /bin/bash


	    
	    #. ${1} -> machine partition (``cox``, ``seas_dgx1``, ``gpu_requeue``)

	    #. ${2} -> Time period (format D-hh:mm)

	    #. ${3} -> Amount of memory for the job (in MB)

	    #. ${4} -> Number of cores

	    #. Request GPU resources: ``--gres=gpu:{num_gpu}``

	#. Using sbatch
	
	    #. ``sbatch`` is used to submit a batch of jobs in the background

		.. code-block:: none

		    #!/bin/bash
		    #SBATCH -n 1                # Number of cores
		    #SBATCH -N 1                # Ensure that all cores are on one machine
		    #SBATCH -t 2-00:00          # Runtime in D-HH:MM, minimum of 10 minutes
		    #SBATCH -p gpu_requeue      # Partition to submit to
                    #SBATCH --gres=gpu:8        # Generic Resources
                    #SBATCH --mem=16000         # Memory pool for all cores (see also --mem-per-cpu) 
                    #SBATCH -o {file where STDOUT will be written}{_%j.out}   # %j inserts jobid
                    #SBATCH -e {file where STDERR will be written}{_%j.err}   # %j inserts jobid
		    
		    module load centos6/0.0.1-fasrc01
		    module load gcc/7.1.0-fasrc01 
		    conda activate mitoskel
		    .........
