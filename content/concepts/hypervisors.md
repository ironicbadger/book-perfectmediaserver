# Virtualization and Containers

A hypervisor is an Operating System designed to primarily do one thing, run virtualisation workloads. That means virtual machines for the most part, though LXC and other containerisation methods are possible too with some hypervisors. 

Virtual machines let you carve up one system into many smaller ones. Containers divide a single VM into small, isolated pieces. The primary difference between a VM and a container is that they consume kernels differently. The kernel is the magic that translates software into commands hardware can understand. When we refer to "Linux", we are technically referring to the Linux kernel.

Each new VM needs it's own, isolated kernel. Containers on the other hand, share one kernel. This means containers use the resources of a system much more efficiently. So surely, VMs are dead then?

## Do I need a hypervisor?

Possibly, it really depends on what you want out of this whole build. If you're looking to just store some files and run a few containers then a hypervisor might be a layer of added complexity that you just don't need. Couple that with the fact that the Linux kernel ships with a hypervisor built-in called KVM and you might have all the virtualisation you need already running *bare metal*. Bare metal meaning you install the OS directly onto the system.

However if you are looking to use this system as more of a 'homelab' type set up then running PMS as a VM on top of a hypervisor might make more sense. Let's say you are running Proxmox right now, a fully open-source Hypervisor, but for some reason work asks you to learn VMware you can [migrate VMs](https://blog.ktz.me/migrate-qcow2-images-from-kvm-to-vmware/) from one platform to the other quite easily.

We've just established portability as a key benefit of running on a hypervisor but what about flexibility? Have you ever been in a situation where you're trying to diagnose why a system won't boot remotely? Unless you have [IPMI](https://www.zenlayer.com/blog/what-is-ipmi/) accessing the console of your system remotely can be really tough. It's worth mentioning at this point that there are projects like [PiKVM](https://github.com/pikvm/pikvm) looking to change this. If running as a VM, console based triage is as simple as opening up another tab in your browser as shown below.

* TODO: Screenshot of Proxmox

Continuing on the flexibility track for a moment we must also consider that not every application or service you might want to experiment with on your LAN is well suited to a container on your primary file serving VM. For example, something like Adguard Home or Home Assistant that needs access to common ports or specific hardware. It's much easier to test out Pihole and Adguard home side by side when you can spin up a couple of VMs in a few clicks.

Then there's snapshots and automation. By running as a VM we can take snapshots at the hypervisor level before any critical tasks like OS upgrades. Not only that, we can use a tools like Ansible or Terraform (see [concepts/infrastructureascode](infra_as_code.md)) to completely automate everything. Or, in the case of a failed upgrade, just rollback the snapshot and it'll be like nothing ever happened.

In our opinion, running as a VM has many, many upsides with only a couple of downsides. The first is that hardware requirements are usually a lot more onerous with virtualisation, especially VMware (more on this shortly). Second, you will need to 'use up' a little of your hardware to run the hypervisor. Usually this is a minimal overhead - with something like Proxmox a couple of GBs of RAM and a dedicated CPU core to the hypervisor will suffice. With VMware you might want to consider running vSphere which needs 4-8gb of RAM but provides the API layer that Ansible and Terraform need to their thing.

Coming back to the more onerous hardware requirements of running on a hypervisor, let's talk about PCI passthrough.

### PCI Passthrough

A game changing technology that gives virtual machines *direct* access to the hardware, [PCI passthrough](https://wiki.archlinux.org/index.php/PCI_passthrough_via_OVMF) enables VMs on the same system to have isolated hardware access. This means performance is broadly identical to if you were using that hardware on bare metal. There are a few key types of devices that are usually worth considering passing through such as SATA controllers, network interfaces and graphics cards. 

An example use case here might be running a media server VM with a disk controller passed through and another VM for an Opnsense firewall with a NIC card. Once inside the VM, the hardware will appear to all intents and purposes as if you were running on bare metal, Opnsense for example now has access to real NICs - so it just *works*. Passthrough is pretty amazing once you get your head around it and unlock the potential of this technology. One box can easily take the role of what used to be a dozen physical machines saving on power bills and hardware purchase costs. In this scenario though, just remember if you reboot your hypervisor for some reason it'll take out your internet too.

I recorded an episode of Linux Unplugged on the topic of passthrough in July 2019, it should clear up any confusion about how specific your hardware choices must be if going this route. That said, things are a little less picky nowadays than they used to be but it pays to thoroughly research components before purchase if this is your desired. Generally speaking, Xeon based systems will give you the least headache as passthrough is a key datacentre technology and is well supported on these platforms.

<div class="d-flex justify-content-center">
<iframe src="https://fireside.fm/player/v2/RUkczH-V+9ZRzR3sB?theme=dark" width="740" height="200" frameborder="0" scrolling="no"></iframe>
</div>

### Other hardware considerations

See [hardware considerations](../hardware/hardware_considerations.md).

