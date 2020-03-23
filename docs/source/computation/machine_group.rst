Group server (hp03 machine)
-------------------------------
- Get account and IP address: ask Admin

- ssh access from a terminal: ``ssh ${IP}``

- Jupyter notebook access from a browser: ``http://${IP}:9999``

- Scripts for Admin

  - create new account::
    
        sudo adduser ${username}
  
  - grant sudo/coxfs access::
    
        sudo usermod -aG sudo ${username}
        sudo usermod -aG coxfs ${username}
  
  - delete account: ``sudo deluser ${username}``

  - list all users: ``cut -d: -f1 /etc/passwd``
