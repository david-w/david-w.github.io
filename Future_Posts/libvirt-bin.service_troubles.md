## libvirt-bin.service troubles.

I've been getting this error for a while now, and decided to troubleshoot.

## The problem:
```sh
● libvirt-bin.service - Virtualization daemon
   Loaded: loaded (/lib/systemd/system/libvirt-bin.service; enabled; vendor preset: enabled)
   Active: activating (auto-restart) (Result: exit-code) since Wed 2019-02-06 13:56:02 EST; 7ms ago
     Docs: man:libvirtd(8)
           http://libvirt.org
  Process: 2699 ExecStart=/usr/sbin/libvirtd $libvirtd_opts (code=exited, status=6)
 Main PID: 2699 (code=exited, status=6)
   CGroup: /system.slice/libvirt-bin.service
           └─2716 /usr/sbin/libvirtd
```
followed by this:
```sh
my_server.my_domain.com systemd[1]: Failed to start Virtualization daemon.
my_server.my_domain.com systemd[1]: libvirt-bin.service: Unit entered failed state.
my_server.my_domain.com systemd[1]: libvirt-bin.service: Failed with result 'exit-code'.
```
