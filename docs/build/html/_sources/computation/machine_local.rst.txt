Local Machine
=====================
Our software packages are developed mainly in python on Linux machines.
For deep learning related functionality, GPUs are required for speed.

- OS

    - Unix-based OS (e.g. Linux, Mac OS X). 
    - **Recommendation**: `Ubuntu LTS <https://www.ubuntu.com/download/desktop>`_

- Install Miniconda (python 3.7): create a new conda env for each project

    - `download <https://conda.io/en/latest/miniconda.html>`_
    - create new env::

        conda create -n ${conda-env-name}

    - activate new env::

        source activate ${conda-env-name}
    
    - deactivate env::

        source deactivate

- Install Jupyter notebook for interactive result display

    - create new kernel (first install ``ipython, ipykernel``)::

          ipython kernel install --user --name ${conda-env-name} --display-name "${display-name}" 

- Mount pfister_lab2 file system to local machine

    - Install packages::

        sudo apt-get install cifs-utils

    - Get your ``gid`` on your local machine: ``id``
    - Create the folder if it doesn't exist ``sudo mkdir /mnt/pfister_lab2``
    - Mount it with your rc username and local machine ``gid``::

        sudo mount -t cifs -o vers=1.0,workgroup=rc,username=${1},gid=${2} \
        //coxfs01.rc.fas.harvard.edu/pfister_lab2 /mnt/pfister_lab2

- Unix Tips

    - Terminal (split screen)

        - On mac: try ``iterm2``
        - On Linux: try ``terminator`` or ``tmux``

    - ssh

        - Automatic login in new bashes (after the login in a bash)
        - Create a file with the following content: ``vim ~/.ssh/config``::

            Host *
              ControlMaster auto
              ControlPath ~/.ssh/master-%r@%h:%p

    - bash	

        - Add useful alias: `vim ~/.bashrc` ::

            alias csh='ssh ${USERNAME}@login.rc.fas.harvard.edu'
