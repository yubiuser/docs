# Route the entire Internet traffic through the WireGuard tunnel

Routing your entire Internet traffic is **optional**, however, it can be advantageous in cases where you are expecting eavesdropping on the network. This may not only happen in insecure open Wi-Fi networks (airports, hotels, trains, etc.) but also in encrypted Wi-Fi networks where the creator of the network can monitor client activity.

Rerouting the Internet traffic through your Pi-hole will furthermore cause all of your Internet traffic to reach the Internet from the place where your WireGuard server is located. This can be used to obfuscate your real location as well as to be allowed to access geo-blocked content, e.g., when your Pi-hole is located in Germany but you are traveling in the United States. If you want to access a page only accessible from within Germany (like the live-broadcast of Tagesschau, etc.), this will typically not work. However, if you route your entire Internet through your Pi-hole, your network traffic will originate from Germany, allowing you to watch the content.

<!-- markdownlint-disable code-block-style -->
!!! info "Create a second profile"
    Instead of editing your existing configuration, you can easily add a new one with the modified `AllowedIPs` line as above. This will give you two tunnel variants and you decide - at any time from mobile - which variant you want. The one with only the DNS traffic being safely forwarded to your Pi-hole or the variant where your entire Internet traffic is encrypted and sent through your Pi-hole. You can choose at any time which is the best solution in your current situation (e.g., trusted network, unencrypted airport Wi-Fi, etc.).
<!-- markdownlint-enable code-block-style -->

<!-- markdownlint-disable code-block-style -->
!!! warning "Ensure you're already forwarding traffic"
    The following assumes you have already prepared your Pi-hole for [IP forwarding](internal.md#enable-ip-forwarding-on-the-server) and [enabled NAT](internal.md#enable-nat-on-the-server). If this is not the case, follow the steps over there before continuing here.
<!-- markdownlint-enable code-block-style -->

To route all traffic through the tunnel to a specific peer, add the default route (`0.0.0.0/0` for IPv4 and `::/0`for IPv6) to `AllowedIPs` in the `[Peer]` section of your clients's WireGuard config files:

``` plain
AllowedIPs = 0.0.0.0/0, ::/0
```

<!-- markdownlint-disable code-block-style -->
??? info "Exemplary client config file with this change"
    ``` plain
    [Interface]
    PrivateKey = [your client's private key]
    Address = [Wireguard-internal IPs of your client, e.g. 10.100.0.2/32, fd08:4711::2/128]
    DNS = 10.100.0.1

    [Peer]
    AllowedIPs = 0.0.0.0/0, ::/0
    Endpoint = [your server's public IP or domain]:47111
    PublicKey = [public key of the server]
    PresharedKey = [pre-shared key of this client]
    PersistentKeepalive = 25
    ```

    The important change is setting the `[Peer] -> AllowedIPs` entry to `0.0.0.0/0, ::/0`

!!! warning "Change this setting only on your clients"
    Do **not** set this on the server in the `[Interface]` section. WireGuard will automatically take care of setting up [correct routing](https://www.wireguard.com/netns/#routing-all-your-traffic) so that networking still functions on all your clients.
<!-- markdownlint-enable code-block-style -->

That's all you need to do. You should use an online check (e.g. www.wieistmeineip.de) to check if your IP changed to the public IP address of your WireGuard server after this change. It is possible to add this change only for a few clients, leaving the others without a full tunnel for all traffic (e.g., where this is not necessary or not desired).

{!abbreviations.md!}
