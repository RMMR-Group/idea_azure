# NXVA30A IDEA on Azure

Key point -- the Linux VM, nested inside Azure and then inside VirtualBox, is not particularly fast. But with some settings changes, Linux builds are tolerable.

1. Create a Windows 10 Professional VM in Azure (I've had success with a Standard D4s v3 (4 vcpus, 16 GiB memory) instance)
1. Install the latest version of MS Visual Studio 2017 Professional (don't need to exactly match the sub-version of VS2017 specified in the manual, even though IDEA will complain). You can get this here https://visualstudio.microsoft.com/vs/older-downloads/
1. Disable Hyper-V within your VM by opening PowerShell and running `Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All`
1. Follow the NXVA30A IDEA installation directions. Make sure you install in drive `C:\`, as IDEA will try to default to drive `D:\` but that's normally a temporary drive that doesn't persist if you shut down the Windows VM.
1. Download and install the latest version of VirtualBox from the web
1. Download and install the latest version of Vagrant from the web
1. In `C:\MIDEA\NXVA30A_162141\MrMake\MrPackagesConfig\mr-linux-vagrant-vbox\Vagrantfile.template`, within the block defining the VirtualBox VM (i.e., under `config.vm.provider :virtualbox do |vb|` insert
```
vb.customize ["modifyvm", :id,
		#"--largepages", "off",
		#"--nestedpaging", "off",
		"--pae", "off"
	]
```
6. Set the VM to 1 or 2 CPUs (2 seems to work fine in the 4 vCPU Azure instance).
7. Disable automatic startup/shutdown of Linux VM
