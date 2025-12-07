Docker was first released in March 2013. It is developed by Solomon Hykes & Sebastien Pahl. 
Docker is a set of platform as a service (PAAS) that uses OS level virtualization whereas VMware uses hardware level virtualization.

<insert vmware vs docker stack image here>

I will touch upon hypervisors breigly as that will help you understand how cloud infra works 
Type-1 Hypervisor runs directly on the system hardware without the need of OS. It is also known as baremetal hypervisor as they run directly on the hardware.

Containers share the host OS kernel, so Windows containers require a Windows kernel, and Linux containers require a Linux kernel. 
Therefore, a Linux host cannot run Windows containers natively, and vice-versa. On Windows, Docker uses a lightweight virtual machine (WSL2 today, Hyper-V historically) to run Linux containers, while Windows containers can run directly on the Windows kernel using Windows Server Container or Hyper-V isolation.
