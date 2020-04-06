Harvard RC server
=======================
The server uses the slurm scheduler `official tutorial <https://www.rc.fas.harvard.edu/resources/running-jobs/>`_.


- `User ID for Harvard RC <https://www.rc.fas.harvard.edu/resources/access-and-login/>`_ 

- Login into RC 

    .. code-block:: none
    
        ssh {username}@login.rc.fas.harvard.edu

- machine partitions option (``-p``): use ``cox``, ``seas_dgx1``, ``gpu_requeue``

- useful ``slurm`` commands

    - ``squeue -u ${username}``: check job status
    - ``scancel -u ${username}``: cancel all your jobs
    - ``scancel ${jobid}``: cancel a specific job

- ``srun``: Get an interactive bash from a machine for debugging 

    - parameters: ${1}=memory in MB, ${2}=# of CPUs, ${3}=# of GPUs
    - request CPU machines::
        
        srun --pty -p cox -t 7-00:00 --mem ${1} -n ${2} /bin/bash

    - request GPU machines::
      
        srun --pty -p cox -t 7-00:00 --mem ${1} -n ${2} --gres=gpu:${3} /bin/bash

- ``sbatch``: Submit batch of jobs in the background 

    .. code-block:: none

        #!/bin/bash
        #SBATCH -n 1                # Number of cores
        #SBATCH -N 1                # Ensure that all cores are on one machine
        #SBATCH -t 2-00:00          # Runtime in D-HH:MM, minimum of 10 minutes
        #SBATCH -p gpu_requeue      # Partition to submit to
        #SBATCH --gres=gpu:1        # Number of GPUs
        #SBATCH --mem=16000         # Memory pool for all cores (see also --mem-per-cpu) 
        #SBATCH -o OUTPUT_FILENAME_%j.out  # %j inserts jobid
        #SBATCH -e OUTPUT_FILENAME_%j.err  # %j inserts jobid
        
        YOUR COMMAND HERE
        .........

- ``sbatch``: use python to generate/submit jobs

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
			cmd+=['python '+cn+' %d '+str(num)+suf]

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


- ssh tunnel for port forwarding (e.g. tensorboard display)

    - Parameters:

        - p1: port number you want to display on localhost
        - p2: port number on RC login server
        - p3: port number on RC compute server (6006 for tensorboard)
        - m1: server name, e.g. coxgpu06

    - Local machine -> RC login server::
      
        ssh -L p1:localhost:p2 xx@login.rc.fas.harvard.edu

    - RC login server -> RC server:: 
      
        ssh -L p2:localhost:p3 m1

    - On RC server:: 
      
        tensorboard --logdir OUTPUT_FOLDER

- Load cuda on rc cluster::
  
    module load cuda/9.0-fasrc02 cudnn/7.0_cuda9.0-fasrc01

- `Harvard VPN <https://docs.rc.fas.harvard.edu/kb/vpn-setup/#VPN_Software_Installation/>`_ 
