Tips
=================================
#. Coding
    #. local machine: local development
    #. rc server: run big jobs
    #. hp03 server: public visualization (html, neuroglancer)
#. Project managment
    #. Create a new conda env for each project
#. Unix Tips
    #. Terminal (split screen)
        #. On mac: try `iterm2`
        #. On Linux: try `terminator` or `tmux`
    #. ssh
        #. Automatic login in new bashes (after the login in a bash)
        #. Create a file with the following content: ``vim ~/.ssh/config``
            ::

                Host *
                  ControlMaster auto
                  ControlPath ~/.ssh/master-%r@%h:%p
    #. bash	
        #. Add useful alias: `vim ~/.bashrc`
            ::

              alias csh='ssh ${USERNAME}@login.rc.fas.harvard.edu'
