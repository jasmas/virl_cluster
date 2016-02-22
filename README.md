THESE STEPS ARE FOR USERS WHO WANT TO RUN 'VIRL CLUSTER on PACKET'.  YOU MUST HAVE A VALID VIRL LICENSE KEY BEFORE ATTEMPTING.

#Steps:

1. On your local VIRL server, run the command

   `sudo salt-call state.sls virl.terraform`
   
   This will install terraform, clone the repo, create an ssh key, copy in minion keys and replace many variables in the variables.tf file.
   
2. Register with www.packet.net for an account

3. Log in to app.packet.net:
  3. Create api key token

4. `cd virl_cluster`

5. edit `passwords.tf` Note: The salt state will generate new keys with each run

6. Edit `settings.tf` to replace the `default` field with your packet_api_key 

7. Edit `variables.tf` and edit the `packet_machine_type` default field to select the machine type (size) that you want

8. Default will be one controller and one compute node.  If you wish for a larger cluster
  1. mv compute2.tf.org compute2.tf
  2. edit bottom of conf/virl.ini to make compute2_active true
  3. edit virl.tf to remove comments for 2 compute nodes, and comment out default compute section

8. Run the command 

   `terraform plan .`
   
   This will validate the terraform .tf file.
   
9. Run the command 

   `terraform apply .`     
   
   This will spin up your Remote VIRL server and install the VIRL software stack. If this runs without errors, expect it to take ~30 minutes. When it completes, the system will report the IP address of your Remote VIRL server. Login using
   
    `ssh root@<ip address>` or `ssh virl@<ip address>`
    
    NOTE - the VIRL server will reboot once the VIRL software has been installed. You must therefore wait until the reboot has completed before logging in.

10. To see more information about your Remote VIRL server, run the command 

   `terraform show` 
   
   The output will provided details of your Remote VIRL server instance.


11. If logged in as `root`, to run commands such as 'nova service-list' you need to be operating as the virl user. To do this, use the command
 
    `su -l virl`

12. The VIRL server is provisioned in a secure manner. To access the server, you must establish an OpenVPN tunnel to the server.
    1. Install an OpenVPN client for your system.
    2. The set up of the remote VIRL server will automatically configure the OpenVPN server. The 'cluster.ovpn' connection profile will be automatically downloaded to the directory from which you ran the `terraform apply .` command. 
    3. The 'cluster.ovpn' file can be copied out to other devices, such as a laptop hosting your local VIRL instance.
    4. Download the file and open it with your OpenVPN client
   
    NOTE - the VIRL server will reboot once the VIRL software has been installed. You must therefore wait until the reboot has completed before bringing up the OpenVPN tunnel.
    
13. With your OpenVPN tunnel up, the VIRL server is available at http://172.16.11.254.
    If using VM Maestro, you must set up the connection profile to point to `172.16.11.254`

14. When you're ready to terminate your remote VIRL server instance, on your LOCAL VIRL server, issue the command 
 
    `terraform destroy .`

To start up again, repeat step 8.

[NOTE] Your uwmadmin and guest passwords are in passwords.tf. If you can't remember them, this is where you can find them, or by running terraform output
 
