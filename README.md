# macOS Unlocker V4 for VMware ESXi

## Unlocker 2007-2023
This project is now archived.

The unlocker should continue to run as there have been few changes to the VMware code in many years.
I have stopped developemnt as I no longer use VMware but would be happy to refer to a fork if someone 
sends me an email with the relevant details.

There is also [Auto Unlocker](https://github.com/paolo-projects/auto-unlocker) which is still active.


## 1. Introduction
Unlocker 4 is designed for VMware ESXi 7.

The Unlocker enables certain flags and data tables that are required to see the macOS type when setting
the guest OS type, and modify the implmentation of the virtual SMC controller device. These capabiltiites are normally 
exposed in Fusion and ESXi when running on Apple hardware.

The patch code carries out the following modifications dependent on the product being patched:

* Patch vmx and derivatives to allow macOS to boot
* Patch libvmkctl.so to allow vCenter to boot macOS guests

It is important to understand that the Unlocker cannot add any new capabilities to VMware ESXi
but enables support for macOS that is disabled in the VMware products when run on non-Apple hardware.

The Unlocker cannot:

* add support for new versions of macOS
* add paravirtualized Apple GPU support 
* add older (non-Ryzen) AMD CPU support

or any other features that are not already in the VMware compiled code. 

## 2. Running the patcher

#### The ESXi unlocker will need to be run each time the ESXi Server is upgraded. 
#### It is also best to switch ESXi to Maintanence mode and make sure you do not have any VMs running.

The code is written in Python and has no pre-requisites and should run directly from the release zip download.

* Download a binary release from https://github.com/DrDonk/esxi-unlocker/releases
* Unload the archive to a folder on the ESXi server datastore and extract the files
* Navigate to the folder with the extracted files

You will then need to run one of the following commands to patch or unpatch the ESXi software.

* unlock - apply patches to VMware ESXi
* relock - remove patches from VMware ESXi
* check  - check the patch status of your VMware installation

## 3. FAQS

### 3.1 Remove older versions of ESXi unlocker
If you hve previously used an earlier version of the ESXi Unlocker please uninstall it by logging into the 
support console and running:

```BootModuleConfig.sh --verbose --remove=unlocker.tgz```

and then rebooting the ESXi server.

### 3.2 AMD CPUs
The ESXi Unlocker does not add support for AMD CPUs, that has to be done via settings for the macOS guest runnning on 
a modern AMD CPU. These settings may allow macOS to run based on tests reported back to the project, but there is no 
formal support for AMD CPUs with the unlocker.

A patched macOS AMD kernel or OpenCore must be used to run on older AMD systems.

1. Read this KB article to learn how to edit a guest's VMX file safely https://kb.vmware.com/s/article/2057902
2. Add the following lines were to the VMX file:
```
cpuid.0.eax = "0000:0000:0000:0000:0000:0000:0000:1011"
cpuid.0.ebx = "0111:0101:0110:1110:0110:0101:0100:0111"
cpuid.0.ecx = "0110:1100:0110:0101:0111:0100:0110:1110"
cpuid.0.edx = "0100:1001:0110:0101:0110:1110:0110:1001"
cpuid.1.eax = "0000:0000:0000:0001:0000:0110:0111:0001"
cpuid.1.ebx = "0000:0010:0000:0001:0000:1000:0000:0000"
cpuid.1.ecx = "1000:0010:1001:1000:0010:0010:0000:0011"
cpuid.1.edx = "0000:0111:1000:1011:1111:1011:1111:1111"
vhv.enable = "FALSE"
vpmc.enable = "FALSE"
vvtd.enable = "FALSE"
```
3. Make sure there are no duplicate lines in the VMX file or the guest will not start and a dictionary error will
   be displayed by VMware.
4. You can now install and run macOS as a guest.

## 4. Thanks
Thanks to Zenith432 for originally building the C++ Unlocker and Mac Son of Knife
(MSoK) for all the testing and support.

Thanks also to Sam B for finding the solution for ESXi 6 and helping me with
debugging expertise. Sam also wrote the code for patching ESXi ELF files and
modified the Unlocker code to run on Python 3 in the ESXi 6.5 environment.

Thanks to lucaskamp for testing the new version 4 of ESXi Unlocker.
