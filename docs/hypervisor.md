# Hosting a Hypervisor

## Motivation

So you have a big, fairly high-tech ( for 2009 ) 1U rack server set-up -- now what?
You could run one gigantic bare-metal single OS, but that would almost certainly be
inflexible and insecure. A more flexible approach, that is still fairly close to metal,
is to run a hypervisor, like [Xen](https://www.xenproject.org/) or [KVM](https://www.linux-kvm.org/page/Main_Page).
