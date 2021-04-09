---
title: Azure PowerShell を使用して Azure Stack Edge Mini R デバイスで VPN を構成する
description: Azure リソースを作成するために Azure PowerShell スクリプトを使用して、Azure Stack Edge Mini R デバイスで VPN を構成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 9fa4c678a04342b47601f81ede7c49ab841f42ba
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630964"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Azure PowerShell を使用して Azure Stack Edge Mini R デバイスで VPN を構成する

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN オプションを指定すると、Azure Stack Edge Mini R または Azure Stack Edge Pro R デバイスから Azure への *TLS* 経由で移動中のデータに対する 2 番目の暗号化レイヤーが提供されます。 Azure portal または Azure PowerShell を使用して、Azure Stack Edge Mini R デバイスで VPN を構成することができます。 

この記事では、クラウドで構成を作成するために Azure PowerShell スクリプトを使用して、Azure Stack Edge Mini R デバイスでポイント対サイト (P2S) VPN を構成するのに必要な手順について説明します。 Azure Stack Edge デバイスでの構成は、ローカル UI を介して行われます。

## <a name="about-vpn-setup"></a>VPN の設定について

P2S VPN ゲートウェイ接続を使用すると、個々のクライアント コンピューターまたは Azure Stack Edge Mini R デバイスから仮想ネットワークへのセキュリティで保護された接続を作成することができます。 クライアント コンピューターまたはデバイスからの P2S 接続を開始します。 この場合の P2S 接続には、標準ベースの IPsec VPN ソリューションである IKEv2 VPN が使用されます。

一般的なワークフローには次の手順が含まれます。

1. 構成の前提条件。
2. Azure で必要なリソースを設定します。
    1. 仮想ネットワークと必要なサブネットを作成して構成します。 
    2. Azure VPN ゲートウェイ (仮想ネットワーク ゲートウェイ) を作成して構成します。
    3. Azure Firewall を設定し、ネットワークとアプリの規則を追加します。
    4. Azure ルーティング テーブルを作成し、ルートを追加します。
    5. VPN ゲートウェイでポイント対サイトを有効にします。
        1. クライアント アドレス プールを追加します。
        2. トンネルの種類を構成します。
        3. 認証の種類を構成します。
        4. 証明書を作成します。
        5. 証明書をアップロードします。
    6. 電話帳をダウンロードします。
3. デバイスのローカル Web UI で VPN を設定します。 
    1. 電話帳を指定します。
    2. サービス タグ (json) ファイルを指定します。


詳しい手順については、次のセクションで説明します。

## <a name="configure-prerequisites"></a>構成の前提条件

- [Azure Stack Edge Mini R デバイスのインストール](azure-stack-edge-mini-r-deploy-install.md)に関するページの手順に従ってインストールされている Azure Stack Edge Mini R デバイスにアクセスできる必要があります。 このデバイスで Azure との P2S 接続が確立されます。 

- Azure で Azure Stack Edge サービスに対して使用可能になっている有効な Azure サブスクリプションにアクセスできる必要があります。 このサブスクリプションを使用して、Azure Stack Edge Mini R デバイスを管理するための対応するリソースを Azure に作成します。  

- Azure Stack Edge Mini R デバイスにアクセスするために使用する Windows クライアントにアクセスできること。 このクライアントを使用して、クラウドに構成をプログラムで作成します。

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
        Azure Stack Edge Mini R デバイスで使用する Azure サブスクリプション名を指定して、VPN を構成します。

    3. クラウドで構成を作成するために必要な[スクリプトをダウンロード](https://aka.ms/ase-vpn-deployment)します。 このスクリプトでは以下を行います。
        
        - Azure 仮想ネットワークと次のサブネットを作成します: *GatewaySubnet*、および *AzureFirewallSubnet*。
        - Azure VPN ゲートウェイを作成して構成します。
        - Azure ローカル ネットワーク ゲートウェイを作成して構成します。
        - Azure VPN ゲートウェイとローカル ネットワーク ゲートウェイの間に Azure VPN 接続を作成して構成します。
        - Azure Firewall を作成し、ネットワークの規則とアプリの規則を追加します。
        - Azure ルーティング テーブルを作成し、それにルートを追加します。

    4. Azure portal で、Azure リソースを作成するリソース グループを作成します。 Azure portal のサービスの一覧に移動し、 **[リソース グループ]** を選んでから **[+ 追加]** を選択します。 サブスクリプションの情報とリソース グループの名前を指定してから、 **[作成]** を選択します。 このリソース グループに移動する場合、現時点ではその下にリソースを含めることはできません。

        ![Azure リソース グループ](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. Azure Stack Edge Mini R デバイスの `.cer` 形式で、Base 64 でエンコードされた証明書が必要になります。 この証明書は、秘密キーと共に `pfx` として Azure Stack Edge デバイスにアップロードする必要があります。 また、この証明書は、P2S 接続を確立しようとしているクライアント上のストアの信頼されたルートにインストールする必要もあります。

## <a name="use-the-script"></a>スクリプトの使用

まず、`parameters-p2s.json` ファイルを変更してパラメーターを入力します。 次に、変更された json ファイルを使用してスクリプトを実行します。

これらの各手順については、以下のセクションで説明します。

### <a name="download-service-tags-file"></a>サービス タグ ファイルをダウンロードする

スクリプトをダウンロードしたフォルダーには、既に `ServiceTags.json` ファイルがある可能性があります。 そうでない場合は、サービス タグ ファイルをダウンロードできます。

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>パラメーター ファイルを変更する

最初の手順として、`parameters-p2s.json` ファイルを変更し、その変更を保存します。 

作成する Azure リソースについては、次の名前を指定します。

|パラメーター名  |説明  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure Virtual Network 名        |
|azureFirewalls_firewall_name     | Azure Firewall 名        |
|routeTables_routetable_name     | Azure ルート テーブル名        |
|publicIPAddresses_VNGW_public_ip_name     | 仮想ネットワーク ゲートウェイのパブリック IP アドレス名       |
|virtualNetworkGateways_VNGW_name    | Azure VPN ゲートウェイ (仮想ネットワーク ゲートウェイ) 名        |
|publicIPAddresses_firewall_public_ip_name     | Azure Firewall のパブリック IP アドレス名         |
|location     |これは、仮想ネットワークを作成するリージョンです。 デバイスに関連付けられているのと同じリージョンを選択します。         |
|RouteTables_routetable_onprem_name| これは追加のルート テーブルの名前です。ファイアウォールで Azure Stack Edge デバイスにパケットをルーティングして戻すのに役立ちます。 このスクリプトでは 2 つの追加のルートを作成し、*default* と *FirewallSubnet* をこのルート テーブルに関連付けます。|

仮想ネットワークと関連するサブネット (*default*、*firewall*、*GatewaySubnet*) を含む、作成される次の Azure リソースの IP アドレスとアドレス空間を指定します。

|パラメーター名  |説明  |
|---------|---------|
|VnetIPv4AddressSpace    | これは、仮想ネットワークに関連付けられているアドレス空間です。 Vnet IP 範囲をプライベート IP 範囲 (https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) として指定します。     |
|DefaultSubnetIPv4AddressSpace    |これは、仮想ネットワークの `Default` サブネットに関連付けられているアドレス空間です。         |
|FirewallSubnetIPv4AddressSpace    |これは、仮想ネットワークの `Firewall` サブネットに関連付けられているアドレス空間です。          |
|GatewaySubnetIPv4AddressSpace    |これは、仮想ネットワークの `GatewaySubnet` に関連付けられているアドレス空間です。          |
|GatewaySubnetIPv4bgpPeeringAddress    | これは BGP 通信用に予約されている IP アドレスであり、仮想ネットワークの `GatewaySubnet` に関連付けられているアドレス空間に基づいています。          |
|ClientAddressPool    | この IP アドレスは、Azure portal での P2S 構成のアドレス プールに使用されます。         |
|PublicCertData     | 公開証明書データは、それに接続している P2S クライアントを認証するために VPN ゲートウェイによって使用されます。 証明書データを取得するには、ルート証明書をインストールします。 証明書が Base 64 でエンコードされており、拡張子が .cer であることを確認します。 この証明書を開き、1 つの連続した行の ==BEGIN CERTIFICATE== と ==END CERTIFICATE== の間にある証明書のテキストをコピーします。     |



### <a name="run-the-script"></a>スクリプトを実行する

これらの手順に従って、変更された `parameters-p2s.json` を使用し、スクリプトを実行して Azure リソースを作成します。

1. PowerShell を実行します。 スクリプトが配置されているディレクトリに切り替えます。

3. スクリプトを実行します。

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > このリリースでは、スクリプトは米国東部の場所でのみ動作します。

    スクリプトを実行するときに、次の情報を入力する必要があります。

    
    |パラメーター  |説明  |
    |---------|---------|
    |場所     |これは、Azure リソースを作成する必要があるリージョンです。         |
    |AzureAppRuleFilePath     | これは `appRule.json` のファイル パスです。       |
    |AzureIPRangesFilePath     |これは、前の手順でダウンロードしたサービス タグの json ファイルです。         |
    |ResourceGroupName     | これは、すべての Azure リソースが作成されるリソース グループの名前です。        |
    |AzureDeploymentName    |これは Azure デプロイの名前です。         |
    |NetworkRuleCollectionName            | これは、作成されて Azure Firewall に追加されるすべてのネットワーク規則のコレクションの名前です。             |
    |優先度            | これは、作成されるすべてのネットワークおよびアプリケーションの規則に割り当てられる優先度です。              |
    |AppRuleCollectionName            |これは、作成されて Azure Firewall に追加されるすべてのアプリケーション規則のコレクションの名前です。                |


    サンプル出力を次に示します。
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - スクリプトの実行には約 90 分かかります。 必ず、スクリプトが開始される直前にネットワークにサインインしてください。
    > - 何らかの理由でスクリプトに失敗したセッションがある場合は、必ず、リソース グループを削除し、その下に作成されたすべてのリソースを削除してください。
  
    
    スクリプトが完了した後、そのスクリプトが存在するのと同じフォルダーにデプロイ ログが生成されます。


## <a name="verify-the-azure-resources"></a>Azure リソースを検証する

スクリプトを正常に実行した後、すべてのリソースが Azure で作成されたことを検証します。 作成したリソース グループに移動します。 以下のリソースが表示されるはずです。

![Azure リソース](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>VPN プロファイルの電話帳をダウンロードする

この手順では、ご利用のデバイスの VPN プロファイルをダウンロードします。

1. Azure portal で、リソース グループにアクセスしてから、前の手順で作成した仮想ネットワーク ゲートウェイを選択します。

    ![Azure Virtual Network ゲートウェイ](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. **[設定]、[ポイント対サイトの構成]** の順に移動します。 **[VPN クライアントのダウンロード]** を選択します。

    ![P2S 構成を有効にする 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. zip 形式のプロファイルを保存し、Windows クライアントに抽出します。

    ![P2S 構成を有効にする 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. *WindowsAmd64* フォルダーに移動してから、次の `.exe` を抽出します: *VpnClientSetupAmd64.exe*。

    ![P2S 構成を有効にする 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. 一時パスを作成します。 次に例を示します。

    `C:\NewTemp\vnet\tmp`

4. PowerShell を実行し、`.exe` が配置されているディレクトリに移動します。 `.exe` を実行するには、次のように入力します。

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. 一時パスには新しいファイルが含まれます。 出力例を次に示します。

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. *.pbk* ファイルは、VPN プロファイルの電話帳です。 これはローカル UI で使用します。


## <a name="vpn-configuration-on-the-device"></a>デバイスでの VPN 構成

Azure Stack Edge デバイスのローカル UI で、これらの手順に従います。

1. ローカル UI で、 **[VPN]** ページに移動します。 [VPN 状態] で、 **[構成]** を選択します。

    ![VPN を構成する 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. **[VPN の構成]** ブレードで、次のようにします。
    
    1. [Upload phone book file]\(電話帳ファイルのアップロード\) で、前の手順で作成した .pbk ファイルを指定します。
    2. [Upload public IP list config file]\(パブリック IP リスト構成ファイルのアップロード\) で、Azure データ センターの IP 範囲の JSON ファイルを入力として指定します。 このファイルは、前の手順で [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) からダウンロードしたものです。
    3. リージョンとして **[eastus]** を選び、 **[適用]** を選択します。

    ![VPN を構成する 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. **[IP address ranges to be accessed using VPN only]\(VPN のみを使用してアクセスする IP アドレス範囲\)** セクションで、Azure Virtual Network 用に選択した Vnet IPv4 の範囲を入力します。

    ![VPN を構成する 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>クライアント接続を検証する

1. Azure portal で、VPN ゲートウェイに移動します。
2. **[設定]、[ポイント対サイトの構成]** の順に移動します。 **[割り当て済み IP アドレス]** に、Azure Stack Edge デバイスの IP アドレスが表示されるはずです。

## <a name="validate-data-transfer-through-vpn"></a>VPN 経由のデータ転送を確認する

VPN が動作していることを確認するには、データを SMB 共有にコピーします。 Azure Stack Edge デバイスで「[共有の追加](azure-stack-edge-gpu-manage-shares.md#add-a-share)」の手順に従います。 

1. クライアント システムにマウントした SMB 共有に、\data\pictures\waterfall.jpg などのファイルをコピーします。 
2. データがコピーされている間に、そのデータが VPN 経由で移動していることを確認するには、次のようにします。

    1. Azure portal で VPN ゲートウェイに移動します。 

    2. **[監視]、[メトリック]** の順に移動します。

    3. 右側のペインで、VPN ゲートウェイとして **[スコープ]** 、ゲートウェイ P2S 帯域幅として **[メトリック]** 、Avg として **[集計]** を選びます。

    4. データのコピー時に、帯域幅の使用率が増え、データのコピーが完了すると、帯域幅の使用率が減ることがわかります。

        ![Azure VPN のメトリック](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. クラウド上のストレージ アカウントにこのファイルが表示されていることを確認します。
 
## <a name="debug-issues"></a>問題をデバッグする

問題をデバッグするには、次のコマンドを使用します。

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

サンプル出力を次に示します。


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

[Azure Stack Edge デバイスでローカル UI を使用して VPN を構成する](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)。