# vmware:iscsimpio

This is a quick note on how to enable iSCSI Multi-Path Input/Output aka MPIO for an VMware iSCSI Datastore.

!!! note

    This how-to will assume you have access to vCenter, but will try to make use of ESXi CLI utilities where possible.


First, make sure SSH and the ESXi Shell are enabled for your VMware ESXi host.

In vCenter do the following:

1. select your host
2. click configure
3. scroll to services
4. click 'Name' to sort alphabetically
5. click the button to select the ESXi Shell, click Start
6. click the button to select SSH, click Start


``` shell title="ssh into esxi host as root"

$ ssh root@esxi.domain.com

```

``` shell title="get listing of storage devices"

$ esxcfg-scsidevs -l

or

$ esxcli storage core path list

```

You want to make a note of the *naa.####' address that is assigned to your iscsi datastore. In run TrueNAS Scale, so mine start with *naa.6589.*

<figure markdown>
![iSCSI NAA ID](/assets/images/vmware/iscsinaa.png){ loading=lazy }
<figcaption>example of my iscsi naa device id</figcaption>
</figure>

Next, we want to set the multipathing policy for the storage device to *round robin*[^1].
[^1]: https://kb.vmware.com/s/article/1011340


In vCenter, do the following:

1. select your vmware host
2. click configure
3. select Storage Devices
4. highlight your iscsi datastore on the right
5. scroll to the very bottom of the Properties tab to Multipathing Policies
6. click the Actions dropdown
7. click Edit Multipathing
8. Click the Path selection policy dropdown
9. choose Round Robin[^2]
10. Click Ok

[^2]: https://kb.vmware.com/s/article/2069356?lang=en_US

Make sure to repeat this for every iscsi datastore and on every individual host.

<figure markdown>
![iSCSI PSP](/assets/images/vmware/iscsipsp.png){ loading=lazy }
<figcaption>example of my iscsi round robin psp</figcaption>
</figure>

Finally, we're going to set our iSCSI PSP Policy to 1 IOPS. This means that 1 IOPS will make use of one of your NICs, the next IOPS will be sent over your 2nd NIC, the next will go over your 1st NIC, and so on. This will cause a drastic increase in datastore throughput, as noted below in my CrystalDiskMark screenshots.

``` shell title="set our psp policy to 1 iops, show device list for confirmation"

$ for i in `esxcfg-scsidevs -c |awk '{print $1}' | grep naa.6589`; \
  do esxcli storage nmp psp roundrobin deviceconfig set --type=iops --iops=1 --device=$i; \
  done && esxcli storage nmp device list

```
=== "MRU"

    | Sequential | 4k |
    | :---------- | -- |
    | ![Sequential](/assets/images/vmware/sequential-mru.gif){ loading=lazy } | ![Sequential](/assets/images/vmware/4k-mru.gif){ loading=lazy }

=== "MPIO"

    | Sequential | 4k |
    | :---------- | -- |
    | ![Sequential](/assets/images/vmware/sequential-mpio.gif){ loading=lazy } | ![Sequential](/assets/images/vmware/4k-mpio.gif){ loading=lazy }