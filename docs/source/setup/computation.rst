Computation
=======================

Harvard RC server (+coxfs/coxgpu)
-------------------------------------------
- Get account (with SEAS account)

  * `RC account <https://www.rc.fas.harvard.edu/resources/access-and-login/>`_.

  *  `coxfs01 access <https://portal.rc.fas.harvard.edu/login/?next=/request/grants/add%3Fsearch%3Dcox_lab>`_.

- Mount coxfs01 file system to local machine

  * Install packages: ``sudo apt-get install cifs-utils``

  * Get your gid on your local machine: ``id``

  * Mount it with your rc username and local machine gid
    ::
    sudo mount -t cifs -o vers=1.0,workgroup=rc,username=${1},gid=${2} \
    //coxfs01.rc.fas.harvard.edu/coxfs01 /mnt/coxfs01

- Submit jobs through slurm scheduler `official tutorial <https://www.rc.fas.harvard.edu/resources/running-jobs/>`_.

  * Get an interactive shell for debug: (${1}: memory in MB, ${2}: # of CPUs, ${3}: # of GPUs)
    + CPU: ``srun --pty -p cox -t 7-00:00 --mem ${1} -n ${2} /bin/bash``
    + GPU: ``srun --pty -p cox -t 7-00:00 --mem ${1} -n ${2} --gres=gpu:${3} /bin/bash``
  * Submit job in the background:
    ``/n/coxfs01/donglai/ppl/public/example_slurm.py``

- Setup CUDA env

  * Request a GPU machine (``srun`` or ``sbatch``)

  * Load cuda on rc cluster: ``module load cuda/9.0-fasrc02 cudnn/7.0_cuda9.0-fasrc01``

- Deep learning env (python3/EM-network): ``source /n/coxfs01/donglai/lib/miniconda2/bin/activate em-net``
- ssh tunnel for port forwarding (e.g. tensorboard display)

  * Parameters:
    # p1: port you want to display on localhost
    # P2: port on rc server
    # M1: coxgpu name, e.g. coxgpu06
  * On local machine: 
    ``ssh -L p1:localhost:p2 xx@login.rc.fas.harvard.edu``
  * On rc login server: ``ssh -L p2:localhost:p2 M1``

Group server (hp03 machine)
-------------------------------
- Get account and IP address: ask Admin
- ssh: ``ssh ${IP}``
- Jupyter notebook: ``http://${IP}:9999``
- install miniconda
  * local copy (py27): ``sh /home/donglai/Downloads/Miniconda2-latest-Linux-x86_64.sh``

  * `download <https://conda.io/en/latest/miniconda.html>`_.
- cmds for neuroglancer
  ::

      screen
      source /home/donglai/miniconda2/bin/activate ng
      python -i xxx.py
