---
comments: true
---

# wireguard:routing:speed

If you've got a box on your home networking acting as a site-to-site router with a wireguard tunnel to the other site and you'r experiencing sub-par routing performance, these tweaks might just be for you.

``` shell title="make sure ethtool is installed"
$ sudo apt install ethtool -y
```

!!! warning

    For these next commands, you want to make sure to run them and disable these features on your physical or virtual NIC interface *as well as* your wireguard interface!

=== "pNIC, vNIC"

    ``` shell title="disable unnecessary offload features"

    $ sudo ethtool --offload eth0 rx off tx off;
    $ sudo ethtool --offload eth0 lro off;
    $ sudo ethtool --offload eth0 tso off;
    $ sudo ethtool --offload eth0 ufo off;
    $ sudo ethtool --offload eth0 gso off;
    $ sudo ethtool --offload eth0 gro off;
    $ sudo ethtool --offload eth0 tx-tcp-segmentation off;
    $ sudo ethtool --offload eth0 tx-tcp-ecn-segmentation off;
    $ sudo ethtool --offload eth0 tx-tcp6-segmentation off

    ```

=== "wireguard interface"

    ``` shell title="disable unnecessary offload features"

    $ sudo ethtool --offload wg0 rx off tx off;
    $ sudo ethtool --offload wg0 lro off;
    $ sudo ethtool --offload wg0 tso off;
    $ sudo ethtool --offload wg0 ufo off;
    $ sudo ethtool --offload wg0 gso off;
    $ sudo ethtool --offload wg0 gro off;
    $ sudo ethtool --offload wg0 tx-tcp-segmentation off;
    $ sudo ethtool --offload wg0 tx-tcp-ecn-segmentation off;
    $ sudo ethtool --offload wg0 tx-tcp6-segmentation off

    ```

!!! tip

    If you still have throughput issues, make sure to set your Wireguard MTU appropriately. This is usually handled automatically, but if not setting it to 1420 or even as low as 1380 can dramatically improve your throughput.
    
Enjoy your much faster routing speeds!
