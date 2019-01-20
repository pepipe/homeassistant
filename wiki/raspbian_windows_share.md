# Get sharing folders from and to Windows machines.

## From Windows to Raspberry Pi
1. Be sure to Turn on file and printer sharing on Windows machine.

1. Share a folder in windows machine

1. Create a folder in Raspberry Pi and connect to windows shared folder
      1. Create a folder: ```mkdir <folder_location>/<folder_name>```
      1. Connect to windows shared folder: 
      ```sudo mount.cifs //<hostname or IP address>/<windows_shared_folder_name> <folder_location>/<folder_name> -o user=<windows_user>```      

## From Raspberry Pi to Windows

### 1. Install Samba package
Update and upgrade apt-get and then install samba and samba-common-bin:

        sudo apt-get update
        sudo apt-get upgrade
        sudo apt-get install samba samba-common-bin

### 2. Configure Samba
1. (Optional) Create a shared folder
```sudo mkdir -m 1777 <folder_location>/<folder_name> ```.
1 prevents the directory from being accidentally deleted and 777 gives everyone read/write/execute permissions on it.

1. Samba config file
```sudo nano /etc/samba/smb.conf```
