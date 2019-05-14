Computation
=======================
In general, we use the Harvard RC cluster for data storage/computation, and local machines or group servers for visualization/jupyter notebook. 


Harvard RC server
-------------------------------------------
The server uses the slurm scheduler `official tutorial <https://www.rc.fas.harvard.edu/resources/running-jobs/>`_.

- machine partitions option (``-p``): use ``cox``, ``seas_dgx1``, ``gpu_requeue``

- ``srun``: Get an interactive bash from a machine for debugging 

        - (${1}: memory in MB, ${2}: # of CPUs, ${3}: # of GPUs)
        - CPU: ``srun --pty -p cox -t 7-00:00 --mem ${1} -n ${2} /bin/bash``
        - GPU: ``srun --pty -p cox -t 7-00:00 --mem ${1} -n ${2} --gres=gpu:${3} /bin/bash``

- ``sbatch``: Submit batch of jobs in the background ``/n/coxfs01/donglai/ppl/public/example_slurm.py``

	.. code-block:: python

		import sys
		opt = sys.argv[1]

		Do = 'db/slurm/'
		def get_pref(mem=10000,do_gpu=False):
			pref = '#!/bin/bash\n'
			pref+='#SBATCH -N 1 # number of nodes\n'
			pref+='#SBATCH -p cox\n'
			pref+='#SBATCH -n 1 # number of cores\n'
			pref+='#SBATCH --mem '+str(mem)+' # memory pool for all cores\n'
			if do_gpu:
				pref+='#SBATCH --gres=gpu:1 # memory pool for all cores\n'
			pref+='#SBATCH -t 4-00:00 # time (D-HH:MM)\n'
			return pref

		cmd=[]
		mem=10000
		do_gpu= False
		if opt =='0': 
			fn='aff' # output file name
			suf = ' \n'
			num = 25;cn = 'classify4-jwr_20um.py'
			cmd+=['source activate pipeline \n'] # activate your conda env
			cmd+=['python /n/coxfs01/donglai/jwr/'+cn+' %d '+str(num)+suf]

		pref=get_pref(mem, do_gpu)+"""
		#SBATCH -o """+Do+"""slurm.%N.%j.out # STDOUT
		#SBATCH -e """+Do+"""slurm.%N.%j.err # STDERR
		"""
		for i in range(num):
			a=open(Do + fn+'_%d.sh'%(i),'w')
			a.write(pref)
			for cc in cmd:
				if '%' in cc:
					a.write(cc%i)
				else:
					a.write(cc)
			a.close()

		# code to run on bash
		print ('for i in {0..%d};do sbatch '+Do+'%s_${i}.sh && sleep 1;done')%(num-1, fn)


- Setup CUDA env

    - Request a GPU machine (``srun`` or ``sbatch``)
    - Load cuda on rc cluster: ``module load cuda/9.0-fasrc02 cudnn/7.0_cuda9.0-fasrc01``

- ssh tunnel for port forwarding (e.g. tensorboard display)

    - Parameters:

        - p1: port you want to display on localhost
        - p2: port on rc server
        - m1: coxgpu name, e.g. coxgpu06

    - On local machine: ``ssh -L p1:localhost:p2 xx@login.rc.fas.harvard.edu``
    - On rc login server: ``ssh -L p2:localhost:p2 m1``


Group server (hp03 machine)
-------------------------------
- Get account and IP address: ask Admin

- ssh: ssh ${IP}

- Jupyter notebook: http://${IP}:9999


Local Machine Setup
-------------------------------
- Install miniconda: create a new conda env for each project

    - local copy (py27): ``sh /home/donglai/Downloads/Miniconda2-latest-Linux-x86_64.sh``
    - `download <https://conda.io/en/latest/miniconda.html>`_

- Install Jupyter notebook for interactive result display

- Mount coxfs01 file system to local machine

    - Install packages: ``sudo apt-get install cifs-utils``
    - Get your ``gid`` on your local machine: ``id``
    - Mount it with your rc username and local machine ``gid``::

        sudo mount -t cifs -o vers=1.0,workgroup=rc,username=${1},gid=${2} \
        //coxfs01.rc.fas.harvard.edu/coxfs01 /mnt/coxfs01

- Unix Tips

    - Terminal (split screen)

        - On mac: try `iterm2`
        - On Linux: try `terminator` or `tmux`

    - ssh

        - Automatic login in new bashes (after the login in a bash)
        - Create a file with the following content: ``vim ~/.ssh/config``
            ::

                Host *
                  ControlMaster auto
                  ControlPath ~/.ssh/master-%r@%h:%p

    - bash	

        - Add useful alias: `vim ~/.bashrc`
            ::

              alias csh='ssh ${USERNAME}@login.rc.fas.harvard.edu'
