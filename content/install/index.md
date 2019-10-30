# Available options

SCIONLab supports three main options to run the SCION services that make up a SCION AS:

1.  [Run a virtual machine](../install/vm.md)

    This is the simplest option to get started and is supported on all platforms that can run Vagrant and VirtualBox.

    Inside the Ubuntu VM, the SCION installation uses packages, identical to the setup described in [Install with a Package Manager](../install/pkg.md).

    The (perhaps obvious) downsides of running in a VM is the implied performance and memory overhead.

2.  [Install with a package manager](../install/pkg.md)

    This is applicable to any recent Ubuntu (or other Debian-based) systems for
    the most common CPU platforms. 
   
    Painless installation procedure and no performance overhead from running a VM.

3.  [Build from sources](../install/src.md)

    This requires following lengthy instructions and installing various development dependencies. 
    Use this option if you want to make modifications to the SCION services.

Additionally, you can [build and run SCION on Android](../install/android.md).
