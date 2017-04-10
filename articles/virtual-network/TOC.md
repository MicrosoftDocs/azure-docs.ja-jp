# 概要
## [仮想ネットワーク](virtual-networks-overview.md)
## [ユーザー定義のルートと IP 転送](virtual-networks-udr-overview.md)
## [仮想ネットワーク ピアリング](virtual-network-peering-overview.md)
## [ビジネス継続性](virtual-network-disaster-recovery-guidance.md)
## [FAQ](virtual-networks-faq.md)
## IP アドレス指定
### [Resource Manager](virtual-network-ip-addresses-overview-arm.md)
### [クラシック](virtual-network-ip-addresses-overview-classic.md)

# 作業の開始
## [最初の仮想ネットワークの作成](virtual-network-get-started-vnet-subnet.md)

# 方法
## 計画と設計
### [仮想ネットワーク](virtual-network-vnet-plan-design-arm.md)
### [ネットワーク セキュリティ グループ](virtual-networks-nsg.md)

## デプロイ
### 仮想ネットワーク
#### [ポータル](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [テンプレート](virtual-networks-create-vnet-arm-template-click.md)
#### [ポータル (クラシック)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (クラシック)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [CLI (クラシック)](virtual-networks-create-vnet-classic-cli.md)

### ネットワーク セキュリティ グループ
#### [ポータル](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [テンプレート](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (クラシック)](virtual-networks-create-nsg-classic-ps.md)
#### [CLI (クラシック)](virtual-networks-create-nsg-classic-cli.md)

### ユーザー定義のルート
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [テンプレート](virtual-network-create-udr-arm-template.md)
#### [PowerShell (クラシック)](virtual-network-create-udr-classic-ps.md)
#### [CLI (クラシック)](virtual-network-create-udr-classic-cli.md)

### 仮想ネットワーク ピアリング
#### [ポータル](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [テンプレート](virtual-networks-create-vnetpeering-arm-template-click.md)

### [ネットワーク インターフェイス](virtual-network-network-interface.md)

### [パブリック IP アドレス](virtual-network-public-ip-address.md)

### 仮想マシン

#### 静的パブリック IP アドレス
##### [ポータル](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [テンプレート](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (クラシック)](virtual-networks-reserved-public-ip.md)

#### 静的プライベート IP アドレス
##### [ポータル](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### [ポータル (クラシック)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (クラシック)](virtual-networks-static-private-ip-classic-ps.md)
##### [CLI (クラシック)](virtual-networks-static-private-ip-classic-cli.md)

#### 複数のネットワーク インターフェイス
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [CLI](virtual-network-deploy-multinic-arm-cli.md)
##### [テンプレート](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell (クラシック)](virtual-network-deploy-multinic-classic-ps.md)
##### [CLI (クラシック)](virtual-network-deploy-multinic-classic-cli.md)

#### 複数の IP アドレス
##### [Azure ポータル](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [テンプレート](virtual-network-multiple-ip-addresses-template.md)

### 接続のシナリオ
#### [仮想ネットワーク (VNet) から VNet に](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet (Resource Manager) から VNet に (クラシック)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet からオンプレミスのネットワークに (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet からオンプレミスのネットワークに (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [高可用性ハイブリッド ネットワーク アーキテクチャ](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### セキュリティのシナリオ
#### [仮想アプライアンスでネットワークをセキュリティで保護する](virtual-network-scenario-udr-gw-nva.md)
#### [Azure とインターネットの間の DMZ](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [クラウド サービスとネットワーク セキュリティ](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [シンプルな DMZ と NSG](virtual-networks-dmz-nsg-asm.md)
##### [DMZ とファイアウォールおよび NSG](virtual-networks-dmz-nsg-fw-asm.md)
##### [DMZ とファイアウォール、UDR、および NSG](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [サンプル アプリケーション](virtual-networks-sample-app.md)

## 構成
### VM の Accelerated Networking
#### [Azure ポータル](virtual-network-accelerated-networking-portal.md)
#### [PowerShell](virtual-network-accelerated-networking-powershell.md)
### [VM のネットワーク スループットを最適化する](virtual-network-optimize-network-bandwidth.md)
### アクセス制御リスト
#### [クラシック ポータル](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)
### [VM とクラウド サービスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)

## 管理
### ネットワーク セキュリティ グループ
#### [ポータル](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [ログ](virtual-network-nsg-manage-log.md)
### 仮想マシン
#### [ホスト名の表示および変更](virtual-networks-viewing-and-modifying-hostnames.md)
#### [VM を別のサブネットに移動する](virtual-networks-move-vm-role-to-subnet.md)

## トラブルシューティング
### ネットワーク セキュリティ グループ
#### [ポータル](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### ルート
#### [ポータル](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [スループットのテスト](virtual-network-bandwidth-testing.md)

# リファレンス
## [PowerShell (Resource Manager)](/powershell/resourcemanager/azurerm.network/v3.4.0/azurerm.network)
## [PowerShell (クラシック)](/powershell/servicemanagement/azure.networking/v3.4.0/azure.networking)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (クラシック)](https://msdn.microsoft.com/library/jj157182.aspx)


# 関連項目
## [Virtual Machines](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# リソース
## [ネットワークのブログ](http://azure.microsoft.com/blog/topics/networking)
## [ネットワークのフォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [料金](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
