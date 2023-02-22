# rclone-onedrive-sync
Using rclone and systemd services to automate OneDrive mount and sync on boot of a Linux desktop.

## Prequisites
- a PC running a Linux distribution with "systemd" services (Arch, Debian, Fedora, OpenSUSE, Ubuntu, etc; Slackware and Gentoo are NOT applicable as they use "SystemV")
- `sudo` permissions on the command line (`sudo <command>`)
- `curl` is already installed (check your distribution's documentation for steps to install `curl`)
- an internet connection

## 1. Install "rclone"
- Open a terminal and issue the following command: `sudo -v; curl https://rclone.org/install.sh | sudo bash`  
- When prompted by `sudo`, type your password and hit enter.  
- Wait a few moments for the script to download and run through its processes; when completed, you should see the following:  
    ```
    rclone <version> has successfully installed.
    Now run "rclone config" for setup. Check https://rclone.org/docs/ for more details.
    ```  

## 2. Run "rclone config"
- In the terminal, issue the following command: `rclone config`  
- You will see the following output:  
    ```
    NOTICE: Config file "/home/username/.config/rclone/rclone.conf" not found - using defaults
    No remotes found, make a new one?
    n) New remote
    s) Set configuration password
    q) Quit config
    n/s/q> 
    ```  
- Type `n` and hit enter.  
- You will be prompted to name your new "remote"; pick something relevant (ex: "OneDrive", "Work-OneDrive", etc) and hit enter; remember this name because you will need it later.  
- You will see a numbered list of options; scroll through the list and find the number corresponding to "Microsoft OneDrive"; type this number and hit enter.  
- When prompted for "OAuth Client Id", just hit enter.  
- When prompted for "OAuth Client Secret", just hit enter.  
- Select the option number for your "region" (more than likely, use option 1 for "Microsoft Cloud Global") and hit enter.  
- When asked to "Edit advanced config?", type `n` and hit enter.  
- When asked to "Use web browser to automatically authenticate rclone with remote?" type `y` and hit enter.  
- Your default web browser will pop up a new window and ask you to sign into Microsoft. Once successful, the browser will let you know to "go back to rclone".  
- Select the numbered option from the list that matches your desired remote drive and hit enter (for this example, use 1 for "OneDrive Personal or Business")
-   Select the numbered option that matches your OneDrive directory (in most cases you will only have one option listed, so just hit enter to accept that default value)  
-   Hit enter through the next two prompts ("Drive OK?" and "Configuration complete")  
-   You will be returned to the starting config prompt, but you will now see your configured "remote" listed:  
    ```
    Current remotes:
    
    Name                 Type
    ====                 ====
    test                 onedrive
    
    e) Edit existing remote
    n) New remote
    d) Delete remote
    r) Rename remote
    c) Copy remote
    s) Set configuration password
    q) Quit config
    e/n/d/r/c/s/q>
    ```  
- Type `q` and hit enter to exit the config utility  

## 3. Create OneDrive mountpoint directory
- You can create this directory anywhere that you have read and write permissions, but I recommend creating in your home directory as follows: `mkdir ~/OneDrive`  
- Issue an `ls -a` command and confirm you see "OneDrive" listed as a directory.  

## 4. Create and activate the systemd service  
- Reference the [onedrive.service](https://github.com/dubsygg/linux-docs/blob/main/rclone-onedrive-sync/onedrive.service) sample file.  
- Switch to root user: `sudo su -`  
- Change directories to your default systemd services directory: `cd /etc/systemd/system/`  
- Using your preferred CLI text editor (nano, vim, etc), create a file called "onedrive.service" (for my example, I prefer vim): `vim onedrive.service`  
- Paste the entire contents of the [onedrive.service](https://github.com/dubsygg/linux-docs/blob/main/rclone-onedrive-sync/onedrive.service) sample file into your text editor; you can right-click and select "Paste" or use `Ctrl+Shift+V`; change the following:  
  - username - change this to your username  
  - usergroup - change this to your user's primary group (more than likely the same as your username)  
  - RemoteName - change this to whatever you named your "remote" during the `rclone config` in Step 2; the name is **Case Sensitive**  
- Save and exit your text editor; verify the "onedrive.service" file exists with an `ls -a` command.  
- Log out of your root session; you can either type `exit` and hit enter, or use `Ctrl+D`  
- Activate the service by issuing the following command: `sudo systemctl enable --now onedrive.service`  
- Confirm the service started correctly with the following command: `systemctl status onedrive.service`; if successful, your output should look similar to the below:  
    ```
    ● onedrive.service - rclone-onedrive-sync
         Loaded: loaded (/etc/systemd/system/onedrive.service; enabled; vendor preset: enabled)
         Active: active (running) since Fri 2023-02-10 10:27:20 EST; 2h 33min ago
       Main PID: 1133 (rclone)
          Tasks: 23 (limit: 76675)
         Memory: 30.3M
            CPU: 6.203s
         CGroup: /system.slice/onedrive.service
                 └─1133 /usr/bin/rclone --vfs-cache-mode writes mount RemoteName: /home/username/OneDrive/ --config /home/username/.config/rclone/rclone.conf
    
    Feb 10 10:27:20 framework systemd[1]: Started rclone_onedrive.
    ```  
- Open your desktop file browser and verify you now have a mounted folder called "OneDrive" with an eject symbol next to it; browse to this folder and verify the folder populates the files and folders list; depending on the size of your OneDrive and how much data is there, it may take a few seconds to index it. Patience is a virtue :)  
- As a final sanity check, reboot your PC and verify once you log back in that the mounted OneDrive directory is still present.  

## 5. (OPTIONAL) Link your Documents directory
- If you have ever accessed your OneDrive using the sync client on a Windows PC, you may notice that it backs up your Desktop, Documents, and Pictures directories; you can accomplish a similar functionality using symbolic links on the command line; for this example, we will ONLY symlink our Documents directory.  
  - this has the potential to destroy data, so please read the following instructions CAREFULLY before proceeding; I am not responsible for loss of your data.  
- Open your desktop file browser and enter your standard Documents directory; if there are any files present, copy them to a flash drive or somewhere easily accessible so we can restore them later.  
- Browse to your mounted OneDrive directory and verify there is also a Documents folder present.  
- Open the terminal and issue the following command: `rm -rf ~/Documents`; this will remove your local Documents directory under your Home directory; verify "Documents" is no longer present with an `ls -a` command.    
- Issue the following command to link your OneDrive Documents to the home directory Documents: `ln -s ~/OneDrive/Documents ~/Documents`; verify with an `ls -a` command that there is now a "Documents" directory in a lighter color than other directories; this indicates the "Documents" directory is a symbolic link to another location.  
- Go back to your desktop file browser and browse to your "Documents" folder under your home directory. You should now see the contents of anything that exists under your OneDrive's "Documents" folder.  

## 6. DISCLAIMERS (WIP)
- This method does not offer an offline sync option like Windows has with "Always keep on my device"; you MUST have an internet connection for the mounted folder to work.  
