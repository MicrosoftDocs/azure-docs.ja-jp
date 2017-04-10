## <a name="virtual-network-basics"></a>Virtual Network の基礎
### <a name="what-is-an-azure-virtual-network-vnet"></a>Azure Virtual ネットワーク (VNet) とは何ですか。
VNet を使用してプロビジョニングして Azure で仮想プライベート ネットワーク (VPN) を管理および、必要に応じて、VNet を Azure で他の VNet とリンクさせたり、オンプレミス IT インフラストラクチャで、ハイブリッドまたはクロスプレミス ソリューションを作成します。 作成したそれぞれの VNet には独自の CIDR ブロックを持っており、他の VNet や、CIDR ブロックが競合しない限りオンプレミス ネットワークにリンクすることができます。 VNet の DNS サーバー設定と、サブネットへの VNet の分割も制御できます。

VNet を使用して次のことが行えます。

* 専用プライベート クラウドのみの仮想ネットワークを作成します。
  
    場合によって、ソリューションの、クロスプレミス構成は必要ありません。 VNet を作成するとき、ご使用のサービスと VNet 内の VMが、クラウド内で互いに直接かつ安全に通信することができます。 これによって、VNet 内にトラフィックが安全に保持されますが、 VM と、ソリューションの一部としてインターネット通信を必要とするサービスとのエンドポイント接続を構成することができます。
* データ センターを安全に拡張します。
  
    VNet については、データ センターの容量を安全にスケールできるよう従来のサイト間 (S2S) VPN を構築できます。 S2S VPN は、IPSEC を使用して、社内の VPN ゲートウェイと Azure 間の安全な接続を提供します。
* ハイブリッド クラウドのシナリオを有効にします。
  
    VNet は、さまざまなハイブリッド クラウド シナリオをサポートする柔軟性を提供します。 メインフレームなどのオンプレミス システムと Unix システムの任意の型へのクラウド ベースのアプリケーションを安全に接続することができます。

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>仮想ネットワークが必要かどうかを知る方法
[Virtual Network の概要](../articles/virtual-network/virtual-networks-overview.md) にアクセスし、最適なネットワーク設計オプションの決定に役立つディシジョン テーブルを参照してください。

### <a name="how-do-i-get-started"></a>開始するには?
[Virtual Network ドキュメント](https://azure.microsoft.com/documentation/services/virtual-network/) にアクセスし開始します。 このページには、一般的な構成手順だけでなく、仮想ネットワークを設計する際に考慮する必要がある内容を理解するのに役立つ情報へのリンクがあります。

### <a name="what-services-can-i-use-with-vnets"></a>VNet でどのようなサービスを使用できますか。
VNet は、Cloud Services (PaaS)、Virtual Machines、Web Apps など、さまざまな異なる Azure サービスで使用できます。 ただし、VNet でサポートされていない、いくつかのサービスがあります。 使用したい特定のサービスをチェックし、互換性があることを確認してください。

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>クロスプレミス接続を使用しないで VNet を使用できますか。
はい。 サイト間接続を使用せずに VNet を使用することができます。 これは、Azure でドメイン コントローラーと SharePoint ファームを実行する場合に特に便利です。

## <a name="virtual-network-configuration"></a>Virtual Network の構成
### <a name="what-tools-do-i-use-to-create-a-vnet"></a>VNet を作成するのには、どのようなツールを使用でしょうか。
作成または仮想ネットワークを構成するのには、次のツールを使用できます。

* Azure ポータル (クラシックおよび Resource Manager VNet)。
* ネットワーク構成ファイル (netcfg - クラシック VNet のみ)。 [ネットワーク構成ファイルを使用した仮想ネットワークの構成](../articles/virtual-network/virtual-networks-using-network-configuration-file.md)を参照してください。
* PowerShell (クラシックおよび Resource Manager VNet)。
* Azure CLI (クラシックおよび Resource Manager VNet)。

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>VNet でどのアドレス範囲が使用できるでしょうか。
パブリック IP アドレスの範囲を使用して、任意の IP アドレス範囲で定義されている [RFC 1918](http://tools.ietf.org/html/rfc1918)です。

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>VNet 内でパブリック IP アドレスを持つことができますか。
はい。 パブリック IP アドレス範囲の詳細については、 [Virtual Network (VNet) でのパブリック IP アドレス空間](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md)を参照してください。 パブリック IP が、インターネットから直接アクセスできないことに留意してください。

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>仮想ネットワーク内のサブネットの数に制限はありますか。
VNet 内で使用できるサブネットの数に制限はありません。 すべてのサブネットは仮想ネットワーク アドレス空間に完全に含める必要があり、また、互いに重複しないようにしてください。

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>これらのサブネット内の IP アドレスの使用に関する制限はありますか。
Azure は、各サブネット内で一部の IP アドレスを予約します。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>VNet およびサブネットは、どれくらい小規模に、また、大規模になるのでしょうか。
サポートする最小のサブネットは /29 、最大は /8 です (CIDR サブネット定義を使用)。

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>VNet を使用して、VLAN を Azure に接続できるでしょうか。
いいえ。 VNet はレイヤー 3 のオーバーレイです。 Azure では、任意のレイヤー 2 のセマンティクスはサポートされません。

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>私の Vnet とサブネットをカスタム ルーティング ポリシーを指定できますか。
はい。 ユーザー定義のルーティング (UDR) を使用することができます。 UDR の詳細については、 [ユーザー定義のルートと IP 転送](../articles/virtual-network/virtual-networks-udr-overview.md)を参照してください。

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNet はマルチキャストやブロードキャストをサポートしますか。
いいえ。 マルチキャストやブロードキャストはサポートされていません。

### <a name="what-protocols-can-i-use-within-vnets"></a>VNet 内はどのようなプロトコルを使用できますか。
VNet 内の標準 IP ベースのプロトコルを使用することができます。 ただし、マルチキャスト、ブロードキャスト、IP-in-IP のカプセル化されたパケット、Generic Routing Encapsulation (GRE) のパケットは、VNet 内でブロックされます。 作動する標準プロトコルは、次のとおりです。

* TCP
* UDP
* ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>VNet 内には、既定ルーターを ping できるでしょうか。
いいえ。

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Tracert を使用して、接続を診断することができますか。
いいえ。

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>VNet を作成した後のサブネットを追加できますか。
はい。 サブネット アドレスが VNet 内の別のサブネットの一部でない限り、サブネットはいつでも VNet に追加できます。

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>作成した後、サブネットのサイズを変更できますか。
PowerShell コマンドレットまたは NETCFG ファイルを使用してその中にデプロイされた VM やサービスがない場合は、サブネットを追加、削除、展開または圧縮できます。 VM やサービスを含むサブネットが変更の影響を受けない限り、プレフィックスを追加、削除、展開、圧縮ができます。

### <a name="can-i-modify-subnets-after-i-created-them"></a>サブネットを作成後に変更できますか。
はい。 VNet で使用された CIDR ブロックを追加、削除、変更することができます。

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>VNet でサービスを実行している場合、インターネットに接続できますか。
はい。 VNet 内にデプロイされているすべてのサービスは、インターネットに接続できます。 Azure にデプロイされたすべてのクラウド サービスには、アドレス指定可能な VIP がパブリックに割り当てられています。 PaaS ロールの入力エンドポイントと仮想マシンのエンドポイントを定義して、これらのサービスがインターネットからの接続を承諾できるようにする必要があります。

### <a name="do-vnets-support-ipv6"></a>VNet は IPv6 をサポートするでしょうか。
いいえ。 この時点で、VNet で IPv6 を使用できません。

### <a name="can-a-vnet-span-regions"></a>VNet は複数のリージョンで広がるでしょうか。
いいえ。 VNet は、1 つのリージョンに制限されます。

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>VNet を Azure での別の VNet に接続できますか。
はい。 REST API または Windows PowerShell を使用して VNet 間の通信を作成できます。 VNet ピアリングを使用して VNet どうしを接続することもできます。 ピアリングの詳細については、[こちら](../articles/virtual-network/virtual-network-peering-overview.md)を参照してください。

## <a name="name-resolution-dns"></a>名前解決 (DNS)
### <a name="what-are-my-dns-options-for-vnets"></a>VNet の DNS オプションとは何でしょうか。
[VM とロール インスタンスの名前解決](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) ページでディシジョン テーブルを使用して、使用できるすべての DNS オプションを行うことができます。

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>VNet の DNS サーバーを指定できますか。
はい。 VNet 設定では、DNS サーバーの IP アドレスを指定できます。 これにより、VNet 内のすべての VM の既定の DNS サーバーとして適用されます。

### <a name="how-many-dns-servers-can-i-specify"></a>DNS サーバーの数を指定できますか。
最大 12 個の DNS サーバーを指定することができます。

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>ネットワークを作成した後、DNS サーバーを変更できますか。
はい。 いつでも、VNet の DNS サーバーの一覧を変更できます。 DNS サーバーの一覧を変更する場合は、新しい DNS サーバーを取得するために VNet で VM をそれぞれ再起動する必要があります。

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Azure で提供される DNS と Vnet と組み合わせて使用について
Azure で提供される DNS は、Microsoft によって提供されるマルチ テナント DNS サービスです。 Azure はこのサービス内のすべての VM とロール インスタンスを登録します。 このサービスは、同じクラウド サービス内に含まれる VM とロール インスタンスのホスト名によって、また、同じ VNet 内の VM とロール インスタンスの FQDN によって名前解決を提供します。

> [!NOTE]
> Azure で提供される DNS を使用して、テナント間の名前解決のため、仮想ネットワーク内の最初の 100 個のクラウド サービスには、この時点で制限があります。 独自の DNS サーバーを使用している場合は、この制限は適用されません。
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>VM ごとに、サービスごとに DNS 設定を上書きできますか。
はい。 クラウド サービスごとに DNS サーバーを設定し、既定のネットワーク設定を上書きすることができます。 ただし、できるだけネットワーク全体の DNS を使用することをお勧めします。

### <a name="can-i-bring-my-own-dns-suffix"></a>独自の DNS サフィックスを取り込むことができますか。
いいえ。 ご使用の VNet のカスタム DNS サフィックスを指定することはできません。

## <a name="vnets-and-vms"></a>VNet および VM
### <a name="can-i-deploy-vms-to-a-vnet"></a>VNet に VM をデプロイできますか。
はい。

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>VNet に Linux VM をデプロイできますか。
はい。 Azure でサポートされている Linux のすべてのディストリビューションをデプロイすることができます。

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>パブリック VIP と内部 IP アドレスの違いとは?
* 内部 IP アドレスは DHCP によって各 VNet 内の VM に割り当てられている IP アドレスです。 パブリック対象ではありません。 VNet を作成した場合は、ご使用の VNet のサブネット設定で指定した範囲から内部 IP アドレスが割り当てられます。 VNet がない場合は、内部 IP アドレスが割り当てられます。 内部 IP アドレスは、VMが割り当て解除されていなければ、VM がある間は共にとどまります。
* パブリック VIP とは、クラウド サービスまたはロード バランサーに割り当てられているパブリック IP アドレスです。 ご使用のVM の NIC に直接割り当てられていません。 VIP は、クラウド サービスが割り当て解除または削除されるまで、割り当てられているクラウド サービスと共にとどまります。 その時点でリリースされます。

### <a name="what-ip-address-will-my-vm-receive"></a>VM が受信するのはどの IP アドレスでしょうか。
* **内部 IP アドレス -** VM を VNet にデプロイする場合は、VM は、指定した内部 IP アドレスのプールから内部 IP アドレスを受信します。 VM は、内部 IP アドレスを使用して VNet 内で通信します。 Azure では動的内部 IP アドレスが割り当てられますが、ご使用の VM の静的アドレスを要求できます。 静的内部 IP アドレスに関する詳細については、 [静的内部 IP アドレスを設定する方法](../articles/virtual-network/virtual-networks-reserved-private-ip.md)参照してください。
* **VIP -** VIP は VM に直接割り当てることはありませんが、ご使用の VM も VIP と関連付けられます。 VIP は、クラウド サービスに割り当てられるパブリック IP アドレスです。 必要に応じて、クラウド サービスの VIP を予約することができます。
* **ILPIP -** インスタンスレベル パブリック IP アドレス (ILPIP) を構成することもできます。 ILPIP はクラウド サービスではなく、VM に直接関連付けられます。 ILPIP の詳細については、「[インスタンスレベル パブリック IP の概要](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)」を参照してください。

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>後で作成する VM の内部 IP アドレスを予約できますか。
いいえ。 内部 IP アドレスを予約することはできません。 内部 IP アドレスが使用可能な場合は、DHCP サーバーによって、VM またはロール インスタンスに割り当てられます。 その VM は、内部 IP アドレスを割り当てさせたいマシンの場合もあるし、そうでない場合もあります。 ただし、すでに作成済みの VM の内部 IP アドレスを、使用可能な内部 IP アドレスに変更することができます。

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>内部 IP アドレスを、VNet 内の VM 用に変更しますか。
はい。 内部 IP アドレスしない限り、VM と有効期間にわたって、VM の割り当てが解除します。 VM が割り当て解除されると、VM の静的内部 IP アドレスを定義しない限り、内部 IP アドレスが解放されます。 VM が単純に停止している場合 ( **停止 (割り当て解除)**状態になっていない)、IP アドレスは、VM に割り当てられたままです。

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>手動で IP アドレスを VM の NIC に割り当てられますか。
いいえ。 VM のインターフェイス プロパティを変更する必要はありません。 どの変更も、VM への接続が失われる可能性があります。

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>VM をシャットダウンする場合、IP アドレスはどうなりますか。
何もありません。 IP アドレス (パブリック VIP と内部 IP アドレスの両方) は、クラウド サービスまたは VM と共にとどまります。

> [!NOTE]
> 単に VM をシャットダウンしたい場合、管理ポータルを使用しないでください。 現時点では、シャットダウン ボタンをクリックすると、仮想マシンの割り当てが解除されます。
> 
> 

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>VNet 内で 1 つのサブネットからもう 1 つのサブネットへと、再デプロイせずに移動できますか。
はい。 詳細については、 [こちらで](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md)確認できます。

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>VM に静的な MAC アドレスを構成できますか。
いいえ。 MAC アドレスを静的に構成することはできません。

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>MAC アドレスは、一度作成されると、VM で同じものとして残りますか。
はい。VM が停止 (割り当て解除) され、再起動された場合でも、VM の MAC アドレスは変わりません。

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>VNet 内の VM からインターネットに接続できますか。
はい。 VNet 内にデプロイされているすべてのサービスは、インターネットに接続できます。 また、Azure にデプロイされたすべてのクラウド サービスには、パブリックにアドレス指定可能な VIP が割り当てられています。 PaaS ロールの入力エンドポイントと vm をインターネットからの接続を受け入れるようにこれらのサービスを有効にするためにエンドポイントを定義する必要があります。

## <a name="vnets-and-services"></a>VNet とサービス
### <a name="what-services-can-i-use-with-vnets"></a>VNet でどのようなサービスを使用できますか。
VNet 内のコンピューティング サービスのみ使用できます。 Compute Services は、Cloud Services (web ロールと worker ロール) および VM に制限されます。

### <a name="can-i-use-web-apps-with-virtual-network"></a>Web Apps を Virtual Network と連動できますか。
はい。 ASE (App Service Environment) を使用して VNet 内に Web Apps をデプロイできます。 さらに、VNet にポイント対サイトの 構成をした場合、Web Apps を安全に接続して、Azure VNet 内のリソースにアクセスできます。 詳細については、次のトピックを参照してください。

* [App Service 環境で Web Apps を作成する](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Web Apps Virtual Network 統合](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)
* [Web Apps と VNet Integration and Hybrid Connections の併用](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)
* [Web アプリを Azure Virtual Network に統合する](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>クラウド サービスを VNet 内の Web ロールと worker ロール (PaaS) でデプロイできますか。
はい。 VNet 内で PaaS サービスをデプロイすることができます。

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>VNet に PaaS ロールをデプロイする方法
これは、サービス構成のネットワーク構成セクションで、VNet の名前と、ロールとサブネットのマッピングを指定することで実現できます。 どのバイナリも更新する必要はありません。

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>サービスを VNet 内外で移動できますか。
いいえ。 サービスを VNet 内外で移動することはできません。 別の VNet に移動するには、サービスを削除し再デプロイする必要があります。

## <a name="vnets-and-security"></a>VNet とセキュリティ
### <a name="what-is-the-security-model-for-vnets"></a>VNet のセキュリティ モデルとは何ですか。
Vnet は、1 つの別の機能と、Azure インフラストラクチャでホストされている他のサービスから完全に分離します。 VNet は、トラスト バウンダリです。

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>VNet で、ACL または NSG を定義できますか。
いいえ。 ACL または NSG を VNet に関連付けることはできません。 ただし、ACL が、VNet にデプロイされている VM の入力エンドポイントで定義されている場合、NSG は、サブネットまたは NIC に関連付けられます。

### <a name="is-there-a-vnet-security-whitepaper"></a>VNet セキュリティに関するホワイト ペーパーはありますか。
はい。 [こちら](http://go.microsoft.com/fwlink/?LinkId=386611)からダウンロードできます。

## <a name="apis-schemas-and-tools"></a>API、スキーマ、およびツール
### <a name="can-i-manage-vnets-from-code"></a>VNet をコードから管理できますか。
はい。 REST API を使用すると、VNet およびクロスプレミス接続を管理することができます。 詳細については、 [こちら](http://go.microsoft.com/fwlink/?LinkId=296833)で確認できます。

### <a name="is-there-tooling-support-for-vnets"></a>VNet に対するツール サポートはありますか。
はい。 各種プラットフォームで PowerShell とコマンド ライン ツールを使用することができます。 詳細については、 [こちら](http://go.microsoft.com/fwlink/?LinkId=317721)で確認できます。



<!--HONumber=Nov16_HO3-->


