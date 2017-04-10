## <a name="tcp-settings-for-azure-vms"></a>Azure VM の TCP 設定
Azure VM は [NAT][nat] (ネットワーク アドレス変換) を使用して、パブリック インターネットと通信を行います。 NAT デバイスは Azure VM にパブリック IP アドレスとポートを割り当て、他のデバイスとの通信に使用するソケットを VM が確立できるようにします。 所定の時間が経過した後、ソケットを通じたパケットの流れが停止すると、NAT デバイスによりマッピングが切断され、ソケットは他の VM が自由に使用できるようになります。

これは一般的な NAT の動作ですが、タイムアウト後もソケットの保持を期待する TCP ベースのアプリケーションでは、通信の問題が発生する場合があります。 *接続が確立された* 状態にあるセッションでは、2 つの考慮すべきアイドル タイムアウト設定があります。

* [Azure Load Balancer][azure-lb-timeout] 経由の**受信**。 このタイムアウトの既定値は 4 分であり、最大 30 分に調整できます。
* [SNAT][snat] (Source NAT) を使用した**送信**。 このタイムアウトは 4 分に設定されており、調整はできません。

タイムアウト制限を超える接続が失われないように、アプリケーションでセッションを有効に保持するか、基になるオペレーティング システムをそのように構成できることを確認してください。 以下のように、使用する設定は Linux と Windows のシステムでは異なります。

[Linux][linux] の場合、次のカーネル変数を変更する必要があります。
net.ipv4.tcp_keepalive_time = 120 net.ipv4.tcp_keepalive_intvl = 30 net.ipv4.tcp_keepalive_probes = 8

[Windows][windows] の場合、次のレジストリ値を変更する必要があります。
(KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8)。

上記の設定では、2 分 (120 秒) のアイドル時間が経過するとキープ アライブ パケットが送信され、その後は 30 秒ごとに送信されます。 これらのパケットのうち 8 つが失敗すると、セッションは切断されます。

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md

<!--HONumber=Jan17_HO3-->


