

#####This article was brought to you by [@kumarallamraju](https://twitter.com/kumarallamraju)

When you first provision an Azure VM, you might have specified a user account like azureuser or something like that. If multiple users require access to this instance, it's a security best practice to use separate accounts for each user.

You can expedite these steps by using cloud-init to add a new user to a Linux VM in Azure. However you need to make sure install cloud-init packages were installed already as stated below before adding users via cloud-init. 

```
sudo yum list installed cloud-init

If cloud-init is not installed, run the following command to install it:

sudo yum install cloud-init

```

In this post we will discuss the manual way of adding additional users to your Azure Linux VM instance. This may not be a scalable approach for adding 10s or 100s of users. In such cases customers typically domain join their VMs using their corporate credentials.
 

#### 1. Create a key pair for the new user account.
The following command creates an SSH key pair using RSA encryption and a bit length of 4096:

``` 
ssh-keygen -m PEM -t rsa -b 4096 

```

#### 2. Add a new user to the Azure VM's linux instance.

- SSH to your VM with the existing user account e.g. ssh kumara@{public-ip-of-the-vm}


- Use the adduser command to add a new user account to an Azure VM Linux instance (replace new_user with the new account name). 

``` 
sudo adduser new_user --disabled-password 

```
(replace new_user with the new account name)
- Change the security context to the new_user account so that folders and files you create will have the correct permissions:

```
sudo su - new_user
```

- Create a .ssh directory in the new_user home directory

```
mkdir .ssh

```
	
- Use the chmod command to change the .ssh directory's permissions to 700. Changing the permissions restricts access so that only the new_user can read, write, or open the .ssh directory.
	
``` 
chmod 700 .ssh

```
	
- Use the touch command to create the authorized_keys file in the .ssh directory:

```
touch .ssh/authorized_keys
```

- Use the chmod command to change the .ssh/authorized_keys file permissions to 600. Changing the file permissions restricts read or write access to the new_user.

	- chmod 600 .ssh/authorized_keys

- Paste the contents of the public key that was generated above in the authorized_keys file

That's it. Now you can SSH to the linux instance with the new_user that was created in the above steps.

```
ssh -i id_rsa new_user@{public-ip-of-the-vm}
```

#### 3. Connect to your instance as the new_user

Run the id command to view the user and group information created for the new_user account:

```
id
```

The id command should return the information similar to the following:

```
uid=1004(new_user) gid=1004(new_user) groups=1004(new_user)
```

#### 4.  Finally distribute the private key file to your new user.


####Conclusion

In this post we learned how to create additional users to login to the Azure VM instance via SSH.


####References


* [Create an SSH public-private key pair for Linux VMs in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys?WT.mc_id=docs-azuredevtips-micrum)























