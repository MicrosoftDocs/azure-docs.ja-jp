# 概要
## [VPN Gateway について](vpn-gateway-about-vpngateways.md)
## [VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)
## [サブスクリプションとサービスの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# 作業の開始
## [VPN Gateway の計画と設計](vpn-gateway-plan-design.md)
## [VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md)
## [VPN デバイスについて](vpn-gateway-about-vpn-devices.md)
## [暗号化の要件について](vpn-gateway-about-compliance-crypto.md)
## [BGP および VPN Gateway について](vpn-gateway-bgp-overview.md)
## [高可用性接続について](vpn-gateway-highlyavailable.md)
## [ポイント対サイト接続について](point-to-site-about.md)

# 方法
## サイト対サイト接続の構成
### [Azure ポータル](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Azure Portal (クラシック)](vpn-gateway-howto-site-to-site-classic-portal.md)
### [クラシック ポータル (クラシック)](vpn-gateway-site-to-site-create.md)

## ポイント対サイト接続の構成 - ネイティブの Azure 証明書認証
### P2S VPN の構成
#### [Azure ポータル](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Azure Portal (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### 自己署名証明書の生成
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [VPN クライアント構成ファイルの作成とインストール](point-to-site-vpn-client-configuration-azure-cert.md)
### [クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)

## ポイント対サイト接続の構成 - RADIUS 認証
### P2S VPN の構成
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [VPN クライアント構成ファイルの作成とインストール](point-to-site-vpn-client-configuration-radius.md)

## VNet 間の接続の構成
### [Azure ポータル](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
### [Azure Portal (クラシック)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## デプロイメント モデル間の VNet 間接続の構成
### [Azure ポータル](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## サイト対サイトおよび ExpressRoute の共存接続の構成
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## 複数のサイト対サイト接続
### [Azure ポータル](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (クラシック)](vpn-gateway-multi-site.md)
## 複数のポリシーベース VPN デバイスの接続
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## 接続に対する IPsec/IKE ポリシーの構成
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## 高可用性のアクティブ/アクティブ接続の構成
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## VPN ゲートウェイ用の BGP の構成
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
## 強制トンネリングについて
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (クラシック)](vpn-gateway-about-forced-tunneling.md)
## ローカル ネットワーク ゲートウェイの設定を変更する
### [Azure ポータル](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [VPN Gateway 接続の確認](vpn-gateway-verify-connection-resource-manager.md)
## [VPN Gateway のリセット](vpn-gateway-resetgw-classic.md)
## VPN ゲートウェイの削除
### [Azure ポータル](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (クラシック)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [VPN ゲートウェイの構成 (クラシック)](vpn-gateway-configure-vpn-gateway-mp.md)
## [ゲートウェイの SKU (旧)](vpn-gateway-about-skus-legacy.md)
## サード パーティ VPN デバイスの構成
### [概要と Azure の構成](vpn-gateway-3rdparty-device-config-overview.md)
### [サンプル: Cisco ASA デバイス (IKEv2/BGP なし)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## トラブルシューティング
### [VNet への VPN スループットの確認](vpn-gateway-validate-throughput-to-vnet.md)
### [コミュニティで推奨される VPN またはファイアウォール デバイスの設定](vpn-gateway-third-party-settings.md)
### [ポイント対サイト接続の問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [サイト対サイト接続が断続的に切断される](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [サイト対サイト接続で接続できない](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 

# リファレンス
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (クラシック)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST ()](/rest/api/network/virtualnetworkgateways)
## [REST (クラシック)](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# 関連項目
## [Virtual Network](/azure/virtual-network/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# リソース
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=networking)
## [ブログ](https://azure.microsoft.com/blog/topics/networking)
## [フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [料金](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
