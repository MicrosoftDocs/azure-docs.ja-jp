> [!div class="op_single_selector"]
> * [ポータル](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI 2.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [CLI 1.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli-nodejs.md)
> * [テンプレート](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

Azure 仮想マシン (VM) には、1 つ以上のネットワーク インターフェイス (NIC) がアタッチされています。 NIC には、1 つ以上の静的または動的パブリックおよびプライベート IP アドレスを割り当てることができます。 VM に複数の IP アドレスを割り当てると、次のことが可能になります。

* 異なる IP アドレスと SSL 証明書を持つ複数のウェブサイトやサービスを、1つのサーバーでホストする。
* ファイアウォールやロード バランサーのような、ネットワーク仮想アプライアンスとして機能する。
* 複数の NIC いずれかの複数のプライベート IP アドレスいずれかを Azure Load Balancer のバックエンド プールに追加する。 以前は、プライマリ NIC のプライマリ IP アドレスのみをバックエンド プールに追加できました。 複数の IP 構成の負荷分散方法の詳細については、[複数の IP 構成の負荷分散](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

VM に接続された各 NIC には、1 つ以上の IP 構成が関連付けられています。 各構成には、1 つの静的または動的プライベート IP アドレスが割り当てられています。 また、1 つのパブリック IP アドレス リソースが関連付けられている場合もあります。 パブリック IP アドレス リソースには、動的または静的なパブリック IP アドレスが割り当てられています。 Azure における IP アドレスの詳細については、「[Azure 内の IP アドレス](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)」を参照してください。 

NIC に割り当てることができるプライベート IP アドレスの数には上限があります。 また、Azure サブスクリプションで使用できるパブリック IP アドレスの数にも上限があります。 詳細については、[Azure の制限](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事を参照してください。
