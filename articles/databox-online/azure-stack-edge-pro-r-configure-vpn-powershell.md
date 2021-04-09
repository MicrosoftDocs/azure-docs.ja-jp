---
title: Azure PowerShell を使用して Azure Stack Edge Pro R デバイスで VPN を構成する
description: Azure リソースを作成するために Azure PowerShell スクリプトを使用して、Azure Stack Edge Pro R デバイスで VPN を構成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 66edd4cad5b2f38696ef1df2030687bf4c7d9956
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634177"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Azure PowerShell を使用して Azure Stack Edge Pro R デバイスで VPM を構成する

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN オプションを指定すると、Azure Stack Edge Pro R デバイスから Azure への *TLS* 経由で移動中のデータに対する 2 番目の暗号化レイヤーが提供されます。 Azure portal または Azure PowerShell を使用して、Azure Stack Edge Pro R デバイスで VPN を構成することができます。

この記事では、クラウドで構成を作成するために Azure PowerShell を使用して、Azure Stack Edge Pro R デバイスで VPN を構成するのに必要な手順について説明します。

## <a name="about-vpn-setup"></a>VPN の設定について

クロスプレミスの VPN 接続は、Azure VPN ゲートウェイとオンプレミスの VPN デバイスおよび両者を接続する IPsec S2S VPN トンネルで構成されます。 一般的なワークフローには次の手順が含まれます。

- 構成の前提条件。
- Azure で必要なリソースを設定します。
    - Azure VPN ゲートウェイ (仮想ネットワーク ゲートウェイ) を作成して構成します。
    - オンプレミスのネットワークと VPN デバイスを表す Azure ローカル ネットワーク ゲートウェイを作成して構成します。
    - Azure VPN ゲートウェイとローカル ネットワーク ゲートウェイの間に Azure VPN 接続を作成して構成します。
    - Azure Firewall を設定し、ネットワークとアプリの規則を追加します。
    - Azure ルーティング テーブルを作成し、ルートを追加します。
- デバイスのローカル Web UI で VPN を設定します。 ローカル ネットワーク ゲートウェイで表されるオンプレミスの VPN デバイスを構成して、Azure VPN ゲートウェイとの実際の S2S VPN トンネルを確立します。

詳しい手順については、次のセクションで説明します。

## <a name="configure-prerequisites"></a>構成の前提条件

1. [Azure Stack Edge Pro R デバイスのインストール](azure-stack-edge-pro-r-deploy-install.md)に関するページの手順に従ってインストールされている Azure Stack Edge Pro R デバイスにアクセスできる必要があります。 このデバイスは、Azure との VPN 接続を作成するためにオンプレミスの VPN デバイスとして機能します。 

2. VPN デバイスには静的パブリック IP アドレス (外部) が必要です。 このアドレスは NAT にしないでください。

3. Azure で Azure Stack Edge サービスに対して使用可能になっている有効な Azure サブスクリプションにアクセスできる必要があります。 このサブスクリプションを使用して、Azure Stack Edge Pro R デバイスを管理するための対応するリソースを Azure に作成します。  

4. Azure Stack Edge Pro R デバイスにアクセスするために使用する Windows クライアントにアクセスできること。 このクライアントを使用して、クラウドに構成をプログラムで作成します。

    1. Windows クライアントに必要なバージョンの PowerShell をインストールするには、次のコマンドを実行します。

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Azure アカウントとサブスクリプションに接続するには、次のコマンドを実行します。

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Azure Stack Edge Pro R デバイスで使用する Azure サブスクリプション名を指定して、VPN を構成します。

    3. クラウドで構成を作成するために必要な[スクリプトをダウンロード](https://aka.ms/ase-vpn-deployment)します。 このスクリプトでは以下を行います。
        
        - Azure 仮想ネットワークと次のサブネットを作成します: *GatewaySubnet*、および *AzureFirewallSubnet*。
        - Azure VPN ゲートウェイを作成して構成します。
        - Azure ローカル ネットワーク ゲートウェイを作成して構成します。
        - Azure VPN ゲートウェイとローカル ネットワーク ゲートウェイの間に Azure VPN 接続を作成して構成します。
        - Azure Firewall を作成し、ネットワークの規則とアプリの規則を追加します。
        - Azure ルーティング テーブルを作成し、それにルートを追加します。


## <a name="use-the-script"></a>スクリプトの使用

まず、`parameters.json` ファイルを変更してパラメーターを入力します。 次に、変更された json ファイルを使用してスクリプトを実行します。

これらの各手順については、以下のセクションで説明します。

### <a name="download-service-tags-file"></a>サービス タグ ファイルをダウンロードする

スクリプトをダウンロードしたフォルダーには、既に `ServiceTags.json` ファイルがある可能性があります。 そうでない場合は、サービス タグ ファイルをダウンロードできます。

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>パラメーター ファイルを変更する

最初の手順として、`parameters.json` ファイルを変更し、その変更を保存します。 


作成する Azure リソースについては、次の名前を指定します。

|パラメーター名  |説明  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure Virtual Network 名        |
|azureFirewalls_firewall_name     | Azure Firewall 名        |
|routeTables_routetable_name     | Azure ルート テーブル名        |
|publicIPAddresses_VNGW_public_ip_name     | 仮想ネットワーク ゲートウェイのパブリック IP アドレス名       |
|virtualNetworkGateways_VNGW_name    | Azure VPN ゲートウェイ (仮想ネットワーク ゲートウェイ) 名        |
|publicIPAddresses_firewall_public_ip_name     | Azure Firewall のパブリック IP アドレス名         |
|localNetworkGateways_LNGW_name    |Azure ローカル ネットワーク ゲートウェイ名          |
|connections_vngw_lngw_name    | Azure VPN 接続の名前。 これは、仮想ネットワーク ゲートウェイとローカル ネットワーク ゲートウェイの間の接続です。       |
|location     |これは、仮想ネットワークを作成するリージョンです。 デバイスに関連付けられているのと同じリージョンを選択します。         |

次の IP アドレスとアドレス空間は、仮想ネットワークとそれに関連するサブネット (default、firewall、GatewaySubnet) を含む、作成される Azure リソースに関連しています。

|パラメーター名  |説明  |
|---------|---------|
|VnetIPv4AddressSpace    | これは、仮想ネットワークに関連付けられているアドレス空間です。       |
|DefaultSubnetIPv4AddressSpace    |これは、仮想ネットワークの `Default` サブネットに関連付けられているアドレス空間です。         |
|FirewallSubnetIPv4AddressSpace    |これは、仮想ネットワークの `Firewall` サブネットに関連付けられているアドレス空間です。          |
|GatewaySubnetIPv4AddressSpace    |これは、仮想ネットワークの `GatewaySubnet` に関連付けられているアドレス空間です。          |
|GatewaySubnetIPv4bgpPeeringAddress    | これは BGP 通信用に予約されている IP アドレスであり、仮想ネットワークの `GatewaySubnet` に関連付けられているアドレス空間に基づいています。          |

次の IP アドレスとアドレス空間は、オンプレミス ネットワーク (Azure Stack Edge Pro R デバイスがデプロイされている場所) に関連します。

|パラメーター名  |説明  |
|---------|---------|
|CustomerNetworkAddressSpace    |  これは、プライベート IP アドレスのアドレス空間です。       |
|CustomerPublicNetworkAddressSpace    |  これは、パブリック IP アドレスのアドレス空間です。       |
|DbeIOTNetworkAddressSpace    |この IP アドレスは、IoT サービスによって予約されています。 このパラメーターは変更しないでください。        |
|AzureVPNsharedKey    |この共有キーは、Azure VPN 接続リソースの作成時に使用されます。 このキーは、ローカル Web UI VPN の構成時に VPN 共有シークレットとしても提供されます。         |
|DBE-Gateway-ipaddress   | Azure Stack Edge Pro R デバイスのパブリック IP アドレス。 これは不明な場合がありますが、プレースホルダー IP アドレスを使用してスクリプトを実行できます。 ローカル ネットワーク ゲートウェイは、実際の IP アドレスを使用して後で編集します。     |

#### <a name="caveats-to-keep-in-mind"></a>注意事項:

- Azure Stack Edge Pro R デバイスの IP アドレスはありません。 プレースホルダー IP アドレスを使用してリソースを作成し、後で Azure ローカル ネットワーク ゲートウェイを変更して、実際のデバイスの IP アドレスと、そのデバイスのローカル ネットワークのアドレス空間を割り当てることができます。
- IANA (Internet Assigned Numbers Authority) の IETF からの指示に基づき、172.16.x.y から 172.24.z.a までの任意のサブネットを使用します。 Azure ネットワーク用に 172.24 の IPv4 アドレス範囲が予約されています。

### <a name="run-the-script"></a>スクリプトを実行する

これらの手順に従って、変更された `parameters.json` を使用し、スクリプトを実行して Azure リソースを作成します。

1. PowerShell を管理者として実行します。

2. スクリプトが配置されているディレクトリに切り替えます。

3. スクリプトを実行します。

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    サンプル出力を次に示します。

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    スクリプトの実行には約 90 分かかります。 スクリプトが完了した後、そのスクリプトが存在するのと同じフォルダーにデプロイ ログが生成されます。


## <a name="verify-the-azure-resources"></a>Azure リソースを検証する

スクリプトを正常に実行した後、すべてのリソースが Azure で作成されたことを検証します。

次に、ご利用のデバイスのローカル Web UI で VPN を構成します。


## <a name="vpn-configuration-on-the-device"></a>デバイスでの VPN 構成

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>VPN を検証する

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>VPN 経由のデータ転送を確認する

VPN が動作していることを確認するには、データを SMB 共有にコピーします。 Azure Stack Edge Pro R デバイスで「[共有の追加](azure-stack-edge-gpu-manage-shares.md#add-a-share)」の手順に従います。 

1. クライアント システムにマウントした SMB 共有に、\data\pictures\waterfall.jpg などのファイルをコピーします。 
2. クラウド上のストレージ アカウントにこのファイルが表示されていることを検証します。

データが VPN 経由で移動していることを確認するには、次のようにします。

1. リソース グループに存在する接続リソースを開きます。 

2. 受信データと送信データの値を確認します。
 
3. リソース グループの Virtual Network ゲートウェイを開きます。 **[トンネルに入った総数]** と **[トンネルから出た総数]** のグラフを表示します。
 
## <a name="debug-issues"></a>問題をデバッグする

問題をデバッグするには、次のコマンドを使用します。

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

サンプル出力を次に示します。


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>次の手順

[Azure Stack Edge Pro R デバイスでローカル UI を使用して VPN を構成する](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
