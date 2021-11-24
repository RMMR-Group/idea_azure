# NXVA30A IDEA on Azure

Key point -- the Linux VM, nested inside Azure and then inside VirtualBox, is not particularly fast. But with some settings changes, Linux builds are tolerable (e.g., ~4 minutes for the Linux build of miniFLASH).

## Creating the VM setup

1. Create a Windows 10 Professional VM in Azure (I've had success with a Standard D4s v3 (4 vcpus, 16 GiB memory) instance)
1. Install the latest version of MS Visual Studio 2017 Professional (don't need to exactly match the sub-version of VS2017 specified in the manual, even though IDEA will complain). You can get this here https://visualstudio.microsoft.com/vs/older-downloads/
1. Disable Hyper-V within your VM by opening PowerShell and running `Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All`
1. Follow the NXVA30A IDEA installation directions. Make sure you install in drive `C:\`, as IDEA will try to default to drive `D:\` but that's normally a temporary drive that doesn't persist if you shut down the Windows VM.
1. Download and install the latest version of VirtualBox from the web
1. Download and install the latest version of Vagrant from the web
1. Turn off nested paging for the Linux VM. This *hugely* accelerates the performance of the Linux VM nested inside Azure. To turn off nested paged, edit `C:\MIDEA\NXVA30A_162141\MrMake\MrPackagesConfig\mr-linux-vagrant-vbox\Vagrantfile.template`, within the block defining the VirtualBox VM (i.e., under `config.vm.provider :virtualbox do |vb|`, inserting
```
vb.customize ["modifyvm", :id,
		"--nestedpaging", "off",
	]
```
*TODO:* Not sure if other tweaks to the VirtualBox paravirtualizaiton settings might help as well, but that's the one I've found so far. 

8. Set the VM to 1 or 2 CPUs (2 seems to work fine in the 4 vCPU Azure instance). Again, edit `C:\MIDEA\NXVA30A_162141\MrMake\MrPackagesConfig\mr-linux-vagrant-vbox\Vagrantfile.template`, and change the relevant line to `machineCPUs = ENV['MRBTUSER_VBOX_CPUS'] || "2"`. *TODO:* Any input on how best to set the environment variable `MRBTUSER_VBOX_CPUS` reliably would be good, as it would be one less edit to this file
1. Disable automatic startup/shutdown of Linux VM. Run `ideacfg -edit` to open the configuration file, and then change relevant lines to
```
VMAutoSuspendAfterBuild=False
VMStopInsteadOfSuspend=False
```

## Using the VM setup

1. To reduce start/stop times for the Linux VM, we've turned of the auto-on/off feature. So, you need to manually run `vboxctrl -start` and `vboxctrl -stop` from wihin the IDEA shell to start/stop the Linux VM.
