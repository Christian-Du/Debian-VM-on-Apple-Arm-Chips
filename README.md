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




<footer>

<!--
  <<< Author notes: Footer >>>
  Add a link to get support, GitHub status page, code of conduct, license link.
-->

---


</footer>
