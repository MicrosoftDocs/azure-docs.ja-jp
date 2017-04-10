---
title: "Application Gateway の作成、起動、または削除 | Microsoft Docs"
description: "このページでは、Azure Application Gateway を作成、構成、起動、および削除する方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 577054ca-8368-4fbf-8d53-a813f29dc3bc
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 119275f335344858cd20b6a17ef87e3ef32b6e12
ms.openlocfilehash: 8b72a3f26e356af588e9f5c2039bcc525366ce11
ms.lasthandoff: 03/01/2017


---
# <a name="create-start-or-delete-an-application-gateway-with-powershell"></a>PowerShell を使用した Application Gateway の作成、起動、または削除 

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
> * [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure Application Gateway はレイヤー&7; のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。 Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。 サポートされている機能の完全な一覧については、「 [Application Gateway の概要](application-gateway-introduction.md)

この記事では、Application Gateway を作成、構成、起動、および削除する手順について説明します。

## <a name="before-you-begin"></a>開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. 既存の仮想ネットワークがある場合は、既存の空のサブネットを選択するか、既存の仮想ネットワークにアプリケーション ゲートウェイ専用の新しいサブネットを作成します。 vnet ピアリングを使用していない限り、アプリケーション ゲートウェイの背後にデプロイするリソースとは異なる仮想ネットワークに、アプリケーション ゲートウェイをデプロイすることはできません。 詳細については、「[VNET ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください
3. 有効なサブネットがある作業用の仮想ネットワークがあることを確認します。 仮想マシンまたはクラウドのデプロイメントでサブネットを使用していないことを確認します。 Application Gateway そのものが、仮想ネットワーク サブネットに含まれている必要があります。
4. アプリケーション ゲートウェイを使用するように構成するサーバーが存在している必要があります。つまり、仮想ネットワーク内にエンドポイントが作成されているか、割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。

## <a name="what-is-required-to-create-an-application-gateway"></a>Application Gateway の作成に必要な構成

`New-AzureApplicationGateway` コマンドを使用してアプリケーション ゲートウェイを作成した時点では、構成は設定されていません。新しく作成したリソースは、XML または構成オブジェクトを使用して構成します。

値は次のとおりです。

* **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。 一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。
* **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

Application Gateway を作成するには:

1. Application Gateway のリソースを作成します。
2. 構成 XML ファイルまたは構成オブジェクトを作成します。
3. 新しく作成した Application Gateway のリソースに構成をコミットします。

> [!NOTE]
> アプリケーション ゲートウェイのカスタム プローブを構成する必要がある場合は、 [PowerShell を使用したカスタム プローブとアプリケーション ゲートウェイの作成](application-gateway-create-probe-classic-ps.md)に関するページを参照してください。 詳細については、 [カスタム プローブと正常性監視](application-gateway-probe-overview.md) に関するページを参照してください。

![Scenario example][scenario]

### <a name="create-an-application-gateway-resource"></a>アプリケーション ゲートウェイのリソースの作成

ゲートウェイを作成するには、`New-AzureApplicationGateway` コマンドレットを使用して、値を独自の値に置き換えて使用します。 この時点ではゲートウェイの課金は開始されません。 課金は後の手順でゲートウェイが正しく起動されたときに開始します。

次の例では、"testvnet1" という仮想ネットワークと "subnet-1" というサブネットを使用してアプリケーション ゲートウェイを作成します。

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

*Description*、*InstanceCount*、および *GatewaySize* は省略可能なパラメーターです。

ゲートウェイが作成されたことを確認するには、`Get-AzureApplicationGateway` コマンドレットを使用します。

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Stopped
VirtualIPs    : {}
DnsName       :
```

> [!NOTE]
> *InstanceCount* の既定値は 2、最大値は 10 です。 *GatewaySize* の既定値は Medium です。 Small、Medium、Large から選択します。

ゲートウェイがまだ起動していないため、*VirtualIPs* と *DnsName* は空白のまま表示されます。 これらの値は、ゲートウェイが実行中の状態になったときに作成されます。

## <a name="configure-the-application-gateway"></a>Application Gateway の構成

Application Gateway は、XML または構成オブジェクトを使用して構成できます。

## <a name="configure-the-application-gateway-by-using-xml"></a>XML を使用して Application Gateway を構成する

次の例では、XML ファイルを使用して、アプリケーション ゲートウェイの設定すべてを構成し、アプリケーション ゲートウェイのリソースにコミットします。  

### <a name="step-1"></a>手順 1

次のテキストをメモ帳にコピーします。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>(name-of-your-frontend-port)</Name>
            <Port>(port number)</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>(name-of-your-backend-pool)</Name>
            <IPAddresses>
                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>(backend-setting-name-to-configure-rule)</Name>
            <Port>80</Port>
            <Protocol>[Http|Https]</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>(name-of-the-listener)</Name>
            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
            <Protocol>[Http|Https]</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>(name-of-load-balancing-rule)</Name>
            <Type>basic</Type>
            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
            <Listener>(name-of-the-listener)</Listener>
            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

構成項目のかっこに囲まれた値を編集します。 拡張子 .xml のファイルに保存します。

> [!IMPORTANT]
> プロトコル項目 HTTP または HTTPS は、大文字小文字を区別します。

次の例では、構成ファイルを使用してアプリケーション ゲートウェイを設定する方法を示します。 この例では、パブリック ポート 80 で HTTP トラフィックの負荷分散を行い、2 つの IP アドレスの間のバックエンド ポート 80 にネットワーク トラフィックを送信します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

### <a name="step-2"></a>手順 2.

次に、Application Gateway を設定します。 `Set-AzureApplicationGatewayConfig` コマンドレットと構成 XML ファイルを使用してください。

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"
```

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>構成オブジェクトを使用して Application Gateway を構成する

次の例では、構成オブジェクトを使用して Application Gateway を構成する方法を示します。 すべての構成項目は個別に構成した後に、アプリケーション ゲートウェイの構成オブジェクトに追加する必要があります。 構成オブジェクトを作成したら、`Set-AzureApplicationGateway` コマンドを使用して、前の手順で作成したアプリケーション ゲートウェイのリソースに構成をコミットします。

> [!NOTE]
> 各構成オブジェクトに値を割り当てる前に、PowerShell でストレージとして使用するオブジェクトの種類を宣言する必要があります。 個々の項目を作成する最初の行では、どの `Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(object name)` を使用するかを定義しています。

### <a name="step-1"></a>手順 1

すべての個別の構成項目を作成します。

次の例で示すように、フロントエンド IP を作成します。

```powershell
$fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
$fip.Name = "fip1"
$fip.Type = "Private"
$fip.StaticIPAddress = "10.0.0.5"
```

次の例で示すように、フロントエンド ポートを作成します。

```powershell
$fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
$fep.Name = "fep1"
$fep.Port = 80
```

バックエンド サーバー プールを作成します。

次の例で示すように、バックエンド サーバー プールに追加する IP アドレスを定義します。

```powershell
$servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
$servers.Add("10.0.0.1")
$servers.Add("10.0.0.2")
```

$server オブジェクトを使用して、バックエンド プール オブジェクト ($pool) に値を追加します。

```powershell
$pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
$pool.BackendServers = $servers
$pool.Name = "pool1"
```

バックエンド サーバー プール設定を作成します。

```powershell
$setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
$setting.Name = "setting1"
$setting.CookieBasedAffinity = "enabled"
$setting.Port = 80
$setting.Protocol = "http"
```

リスナーを作成します。

```powershell
$listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
$listener.Name = "listener1"
$listener.FrontendPort = "fep1"
$listener.FrontendIP = "fip1"
$listener.Protocol = "http"
$listener.SslCert = ""
```

ルールを作成します。

```powershell
$rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
$rule.Name = "rule1"
$rule.Type = "basic"
$rule.BackendHttpSettings = "setting1"
$rule.Listener = "listener1"
$rule.BackendAddressPool = "pool1"
```

### <a name="step-2"></a>手順 2.

Application Gateway の構成オブジェクト ($appgwconfig) にすべての個別の構成項目を割り当てます。

フロントエンド IP を構成に追加します。

```powershell
$appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
$appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
$appgwconfig.FrontendIPConfigurations.Add($fip)
```

フロントエンド ポートを構成に追加します。

```powershell
$appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
$appgwconfig.FrontendPorts.Add($fep)
```
バックエンド サーバー プールを構成に追加します。

```powershell
$appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
$appgwconfig.BackendAddressPools.Add($pool)
```

バックエンド プールの設定を構成に追加します。

```powershell
$appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
$appgwconfig.BackendHttpSettingsList.Add($setting)
```

リスナーを構成に追加します。

```powershell
$appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
$appgwconfig.HttpListeners.Add($listener)
```

ルールを構成に追加します。

```powershell
$appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
$appgwconfig.HttpLoadBalancingRules.Add($rule)
```

### <a name="step-3"></a>手順 3.
`Set-AzureApplicationGatewayConfig` を使用して、アプリケーション ゲートウェイのリソースに構成オブジェクトをコミットします。

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig
```

## <a name="start-the-gateway"></a>ゲートウェイの起動

ゲートウェイを構成したら、 `Start-AzureApplicationGateway` コマンドレットを使用してゲートウェイを起動します。 Application Gateway の課金は、ゲートウェイが正常に起動された後に開始します。

> [!NOTE]
> `Start-AzureApplicationGateway` コマンドレットの実行には最大で 15 ～ 20 分かかる場合があります。

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>ゲートウェイの状態の確認

`Get-AzureApplicationGateway` コマンドレットを使用してゲートウェイの状態を確認します。 前の手順で `Start-AzureApplicationGateway` が成功した場合、*State* は Running になり、*Vip* と *DnsName* に有効な値が入ります。

次の例では、起動に成功し、実行中で、 `http://<generated-dns-name>.cloudapp.net`方向のトラフィックを受け入れる準備が完了しているアプリケーション ゲートウェイを示します。

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
Vip           : 138.91.170.26
DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net
```

## <a name="delete-an-application-gateway"></a>Application Gateway の削除

Application Gateway を削除するには:

1. `Stop-AzureApplicationGateway` コマンドレットを使用してゲートウェイを停止します。
2. `Remove-AzureApplicationGateway` コマンドレットを使用してゲートウェイを削除します。
3. `Get-AzureApplicationGateway` コマンドレットを使用して、ゲートウェイが削除されたことを確認します。

このサンプルの最初の行は `Stop-AzureApplicationGateway` コマンドレットを示し、その後に出力が続きます。

```powershell
Stop-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

アプリケーション ゲートウェイが停止状態になったら、`Remove-AzureApplicationGateway` コマンドレットを使用してサービスを削除します。

```powershell
Remove-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

サービスが削除されていることを確認するには、`Get-AzureApplicationGateway` コマンドレットを使用します。 この手順は必須ではありません。

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
.....
```

## <a name="next-steps"></a>次のステップ

SSL オフロードを構成する場合は、 [SSL オフロード用のアプリケーション ゲートウェイの構成](application-gateway-ssl.md)に関するページを参照してください。

内部ロード バランサーと共に使用するようにアプリケーション ゲートウェイを構成する場合は、「 [内部ロード バランサー (ILB) を使用したアプリケーション ゲートウェイの作成](application-gateway-ilb.md)」を参照してください。

負荷分散のオプション全般の詳細については、次を参照してください。

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png

