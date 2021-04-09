---
title: Azure Stack Edge Mini R Wi-Fi 管理
description: Azure portal を使用して Azure Stack Edge での Wi-Fi を管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/28/2020
ms.author: alkohli
ms.openlocfilehash: f00be43d023d912d4b0b6e825dfe9d3e0ca2d250
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96465743"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>ローカル Web UI を使用して Azure Stack Edge Mini R でのワイヤレス接続を管理する

この記事では、Azure Stack Edge Mini R デバイスでのワイヤレス ネットワーク接続を管理する方法について説明します。 Azure Stack Edge Mini R デバイス上のローカル Web UI を使用して、Wi-Fi プロファイルを追加したり、それに接続したり、それを削除したりできます。

## <a name="about-wi-fi"></a>Wi-Fi について

Azure Stack Edge Mini R デバイスは、ネットワークに接続されている状態でも、ワイヤレス ネットワーク経由でも動作します。 デバイスには Wi-Fi ポートが用意されています。デバイスをワイヤレス ネットワークに接続できるようにするには、これを有効にする必要があります。 

ご利用のデバイスには 5 つのポートがあります (ポート 1 からポート 4、5 番目は Wi-Fi ポート)。 次に示すのは、ワイヤレス ネットワークに接続されている場合の Mini R デバイスのバック プレーンの図です。

![Wi-Fi 用のケーブル接続](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Wi-Fi プロファイルを追加して接続する

Wi-Fi プロファイルを追加してそれに接続するには、ご利用のデバイスのローカル UI で次の手順を行います。

1. デバイスのローカル Web UI で **[開始]** ページに移動します。 **[ネットワーク]** タイルで **[構成]** を選択します。  
    
    ご利用の物理デバイスには 5 つのネットワーク インターフェイスがあります。 ポート 1 とポート 2 は、1 Gbps ネットワーク インターフェイスです。 ポート 3 とポート 4 は、どちらも 10 Gbps ネットワーク インターフェイスです。 5 番目のポートは Wi-Fi ポートです。 

    [![ローカル Web UI の [ネットワーク設定] ページ 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Wi-Fi ポートを選択し、ポート設定を構成します。 
    
    > [!IMPORTANT]
    > Wi-Fi ポートに対して静的 IP アドレスを構成することを強くお勧めします。  

    ![ローカル Web UI の [ネットワーク設定] ページ 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Wi-Fi ポートの設定を適用すると、 **[ネットワーク]** ページが更新されます。

    ![ローカル Web UI の [ネットワーク設定] ページ 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. **[Add Wi-Fi profile]\(Wi-Fi プロファイルの追加\)** を選択して、使用する Wi-Fi プロファイルをアップロードします。 

    ![ローカル Web UI のポート WiFi の [ネットワーク設定] 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    ワイヤレス ネットワーク プロファイルには、ワイヤレス ネットワークに接続するために必要な SSID (ネットワーク名)、パスワード キー、およびセキュリティ情報が含まれています。 ご利用の環境の Wi-Fi プロファイルは、ネットワーク管理者から入手できます。

    ![ローカル Web UI のポート WiFi の [ネットワーク設定] 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    プロファイルが追加されると、Wi-Fi プロファイルの一覧が更新されて新しいプロファイルが反映されます。 プロファイルには、 **[接続状態]** が **[Disconnected]\(切断済み\)** と表示されています。 

    ![ローカル Web UI のポート WiFi の [ネットワーク設定] 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. ワイヤレス ネットワーク プロファイルが正常に読み込まれたら、このプロファイルに接続します。 **[Connect to Wi-Fi profile]\(Wi-Fi プロファイルに接続する\)** を選択します。 

    ![ローカル Web UI のポート Wi-Fi の [ネットワーク設定] 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. 前の手順で追加した Wi-Fi プロファイルを選択し、 **[適用]** を選択します。 

    ![ローカル Web UI のポート Wi-Fi の [ネットワーク設定] 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **[接続状態]** が **[接続完了]** に更新されます。 信号の品質を示す信号強度が更新されます。 

    ![ローカル Web UI のポート Wi-Fi の [ネットワーク設定] 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > 大量のデータを転送する場合は、ワイヤレス ネットワークではなく、ワイヤード (有線) 接続を使用することをお勧めします。 


## <a name="download-wi-fi-profile"></a>Wi-Fi プロファイルをダウンロードする

ワイヤレス ネットワーク接続に使用している Wi-Fi プロファイルは、ダウンロードすることができます。

1. デバイスのローカル Web UI で、 **[構成] > [ネットワーク]** の順に移動します。 

2. [Wi-Fi プロファイルの設定] で、 **[プロファイルのダウンロード]** を選択します。 これにより、現在使用している Wi-Fi プロファイルがダウンロードされます。


## <a name="delete-wi-fi-profile"></a>Wi-Fi プロファイルを削除する

ワイヤレス ネットワーク接続に使用している Wi-Fi プロファイルは、削除することができます。


1. デバイスのローカル Web UI で、 **[構成] > [ネットワーク]** の順に移動します。 

2. [Wi-Fi プロファイルの設定] で、 **[Wi-Fi プロファイルの削除]** を選択します。

3. **[Wi-Fi プロファイルの削除]** ブレードで、削除するプロファイルを選択します。 **[適用]** を選択します。


## <a name="configure-cisco-wi-fi-profile"></a>Cisco Wi-Fi プロファイルを構成する

ここでは、デバイス上で Cisco ワイヤレス コントローラーおよびアクセス ポイントを管理および構成する方法に関するガイダンスを示します。 

### <a name="dhcp-bridging-mode"></a>DHCP ブリッジ モード

ご利用のデバイスに Cisco ワイヤレス コントローラーを使用するには、ワイヤレス LAN コントローラー (WLC) 上で動的ホスト構成プロトコル (DHCP) のブリッジ モードを有効にする必要があります。

詳細については、「[DHCP Bridging Mode](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9)」(DHCP ブリッジ モード) を参照してください。

#### <a name="bridging-configuration-example"></a>ブリッジ構成の例

コントローラー上で DHCP ブリッジ機能を有効にするには、そのコントローラー上で DHCP プロキシ機能を無効にする必要があります。 コマンド ラインを使用して DHCP ブリッジを有効にするには、次のようにします。

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

クライアントと同じレイヤー 2 (L2) ネットワーク上に DHCP サーバーが存在しない場合は、IP ヘルパーを使用して、クライアント ゲートウェイにある DHCP サーバーにブロードキャストを転送する必要があります。 この構成の例を次に示します。

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

DHCP ブリッジ機能はグローバル設定であるため、コントローラー内のすべての DHCP トランザクションにその影響が及びます。 コントローラー上で必要なすべての仮想ローカル エリア ネットワーク (VLAN) 用のワイヤード (有線) インフラストラクチャに IP ヘルパー ステートメントを追加する必要があります。

### <a name="enable-the-passive-client-for-wlan"></a>WLAN のパッシブ クライアントを有効にする

Cisco ワイヤレス コントローラー上のワイヤレス ローカル エリア ネットワーク (WLAN) に対してパッシブ クライアント機能を有効にするには、次のようにします。

* WLAN に関連付けられたインターフェイスでは、VLAN タグ付けが有効になっている必要があります。
* WLAN に対してマルチキャスト VLAN を有効にする必要があります。
* WLC 上で GARP 転送を有効にする必要があります。

詳細については、[マルチキャストの最適化に関するマルチキャスト VLAN 情報](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html)に関するページを参照してください。

### <a name="troubleshoot"></a>トラブルシューティング

Azure Stack Edge Mini R デバイス上で稼働している VM において IP アドレスの割り当てに関する問題が発生した場合は、ネットワーク環境での上記の構成設定を検証する必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Mini R デバイスをデプロイする](azure-stack-edge-mini-r-deploy-prep.md)方法について説明します。
