# Virtual Image

The project builds an OVA version of the Generic \(x86\_64\) imported which can be imported into current versions of vmware Workstation, Fusion, and vSphere \(ESXi\). The OVA exists to support some development activities, e.g. functional testing of userspace changes and the LibreELEC settings add-on without needing to use HTPC hardware.

We have never formally supported running LibreELEC in a hypervisor. This is mainly because none of the active project maintainers do this, so when software breakage to the OVA occurs the break often goes undiscovered and unfixed for many months \(until the next time some development needing it takes place\).

As we do not support the current image, there are no plans to extend virtual use to Hyper-V, KVM, QEMU and other &lt;insert name&gt; hypervisors. 

