<header>
  
## Debian VM on Apple Silicon to use Docker without Desktop

_Step by step how to set up a Debian VM in UTM, enable port forwarding in UTM and in Debian and forward docker commands to use docker like navite on your Mac :technologist:_

</header>


## Step 1: Install UTM

Installing [UTM](https://github.com/utmapp/UTM) on your Mac is pretty easy. You can download the .dmg file from the [UTM website](https://mac.getutm.app/) or install the App from the App Store. If you download the App from the website it's free. I first installed UTM form the page for free and later still purchased it from the App Store, because I think they are doing a great job and it's worth supporting them.
<img width="900" alt="image" src="https://github.com/user-attachments/assets/20dece9d-229a-4cc4-986f-3bc22e66af83" />

## Step 2: Creating the VM

Download a Debian Netinstall iso - make sure it's the arm64 Version.
Start the UTM App, click "Create a New Virtual Machine", "Virtualize", "Other". Click "Browse" and pick your downloaded Debian iso. Continue by selecting the Hardware for your VM. I don't know what you are up to with your VM and Docker but I recomend not going under 2GB of RAM and 20 GB of storage, you could but it can become a tight sqeeze fast even with this settings. For now we stay with one network interface for our VM. Follow the guided installation of debian. When it comes to the software selection we only need the SSH server and standard system utilitys.
<img width="1835" alt="image" src="https://github.com/user-attachments/assets/74e6c015-1aa5-4e0d-9334-5a9888c0bc72" />
Finish the installation and reboot.

In my case the VM ist direktly starting back into installation device. So time to remove it. Right Click on your VM in UTM, klcik "edit" and delete the drive with the installation iso.
<img width="1012" alt="image" src="https://github.com/user-attachments/assets/8ac279e7-981b-464e-8351-833c2dff5338" />

## Step 3: Set up network in UTM and VM

To set up the ssh we need some port forwarding from the Mac to the VM to achive that open the edit window for your VM, create a new network device by clicking New under Devices. Important leave the old one as it is, thats how your VM get's it's internet access.
Select Network Mode "Emulated VLAN".
<img width="1012" alt="image" src="https://github.com/user-attachments/assets/38d87987-3d80-417f-bd45-fe7285e25a6d" />

With that ou should see the Port Forwarding. Add a new rule, select TCP, 22 for the guest port and 2222 for your host port (you can choose another port if 2222 is not available on your machine). Save settings an boot your VM and log in to it.
<img width="1024" alt="image" src="https://github.com/user-attachments/assets/89d12f31-e257-45a6-b3e9-f1c1928d65bb" />

## Step 4: Set up network in your VM

When I made my first attempts at the approach we are using here, I couldn't log into the VM, even though everything seemed to be set up correctly. I'll guide you through the setup on your Debian VM so you don't have to figure it out by yourself.
When you type "ip a" in your VM you'll see something like this:
<img width="868" alt="image" src="https://github.com/user-attachments/assets/ffa3c5e2-adc9-418b-9ae4-b8d231c29125" />
There is 1 your loopback adress and more important for now two more network interfaces, in my case it's enp0s1 and enp0s2 enp0s1 is up and fine but enp0s2 state is DOWN. I won't presume to judge whether it's a bug or a feature, but we need enp0s2. So what to do? First activate enp0s2 with "sudo ip link set enp0s2 up" and "sudo dhclient enp0s2".
<img width="912" alt="image" src="https://github.com/user-attachments/assets/8890f158-ce35-49c6-bdbe-60c3f92274ca" />

This is the first time I can log into the new VM with ssh. Type "ssh -p 2222 your-vm-username@127.0.0.1" and you will see something like this:
<img width="1377" alt="image" src="https://github.com/user-attachments/assets/9fad234d-00df-434b-ae54-f8e38283e7c7" />

In my case this worked fine but didn't survieve the reboot. Let's fix that next.
Type "sudo crontab -e" (make sure you are doing it with sudo) pick the editor of your choice, (pick nano if you have no expirence in using editors on the command line).
<img width="1377" alt="image" src="https://github.com/user-attachments/assets/7cae442f-5c54-4b99-a01d-065bd6bfe521" />

next you will see the editor window for your cron jobs. Add the line "@reboot sudo ip link set enpls2 up && sudo dhclient enpos2" and save.

<img width="1378" alt="image" src="https://github.com/user-attachments/assets/30f4b610-9912-4d19-bb81-1c9e4d5dd089" />

With that changes done you shoul be able to access your VM after a reboot from your Mac Terminal. 

## Step 4: Set up Ssh to login without user interaction

Start your terminal on your Mac and cd into your  ~/.ssh/ .
Generate a ssh key with "ssh-keygen -f your-vm-key". In this case it dosn't matter what algorythm you use to generate the key. If you are up to use your outside just your physical machine it's worth reading [this](https://www.ssh.com/academy/ssh/keygen#choosing-an-algorithm-and-key-size).
Now let's add the key we just generated to the VM. You still have to be in your ~/.ssh/ folder, then type "ssh-copy-id -p 2222 -i your-vm-key christian@127.0.0.1" enter the password for your vm and with that you added the key to your machine.
<img width="1336" alt="image" src="https://github.com/user-attachments/assets/130ae25a-4a92-49c1-9223-013c98f48a91" />

Next we will conifigure your ssh on your Mac. Still in your ~/.shh folder edit the config file with "nano config" and add the folowing entry:

Host your-vm
   HostName 127.0.0.1
   User christian # make sure to use your VM username
   Port 2222
   IdentityFile ~/.ssh/your-vm-key

save and exit.

Now you can login to your vm with "ssh your-vm" (or however you called the Host in your ~/.ssh/config)
<img width="1376" alt="image" src="https://github.com/user-attachments/assets/4b32dac4-b0fa-479a-bc85-cd415759dc0e" />

## Step 5: Install Docker

There is a really good guide on how to install Docker on [https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/). Make sure you do the postinstall stuff [here](https://docs.docker.com/engine/install/linux-postinstall/) as well.

## Step 6: Add docker alias to your ~/.zshrc or ~/.bashrc

Last one is a easy one. In your "~/.zshrc" add the line alias docker="ssh docker docker"
<img width="1378" alt="image" src="https://github.com/user-attachments/assets/e14d9b69-88f7-4801-9bdf-e2597e9621aa" />
Now do "source ~/.zshrc".
When you have done the last stepe you can now test your setup with with "docker run hello-world"
<img width="1378" alt="image" src="https://github.com/user-attachments/assets/2b36d49b-a78c-4c9a-a519-42d922abf821" />

<footer>

<!--
  <<< Author notes: Footer >>>
  Add a link to get support, GitHub status page, code of conduct, license link.
-->


</footer>
