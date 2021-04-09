---
title: クラシック デプロイ モデルを使用してカスタム プローブを作成する - Azure Application Gateway
description: クラシック デプロイ モデルで PowerShell を使用して、Application Gateway のカスタム プローブを作成する方法の説明
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 13441899eeb5ca2b7c60977ab2858fe40a398d1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397860"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>PowerShell を使用して Azure Application Gateway (クラシック) のカスタム プローブを作成する

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-probe-classic-ps.md)

この記事では、PowerShell を使用して既存の Application Gateway にカスタム プローブを追加します。 カスタム プローブは、特定の正常性チェック ページがあるアプリケーションや、既定の Web アプリケーションに対して正常な応答を返さないアプリケーションに役立ちます。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../azure-resource-manager/management/deployment-models.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](application-gateway-create-probe-ps.md)方法について説明します。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

Application Gateway を作成するには:

1. Application Gateway のリソースを作成します。
2. 構成 XML ファイルまたは構成オブジェクトを作成します。
3. 新しく作成した Application Gateway のリソースに構成をコミットします。

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>カスタム プローブを設定した Application Gateway リソースの作成

ゲートウェイを作成するには、`New-AzureApplicationGateway` コマンドレットを使用して、値を独自の値に置き換えて使用します。 この時点ではゲートウェイの課金は開始されません。 課金は後の手順でゲートウェイが正しく起動されたときに開始します。

次の例では、"testvnet1" という仮想ネットワークと "subnet-1" というサブネットを使用してアプリケーション ゲートウェイを作成します。

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

ゲートウェイが作成されたことを確認するには、`Get-AzureApplicationGateway` コマンドレットを使用します。

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> *InstanceCount* の既定値は 2、最大値は 10 です。 *GatewaySize* の既定値は Medium です。 Small、Medium、Large から選択します。
> 
> 

ゲートウェイがまだ起動していないため、*VirtualIPs* と *DnsName* は空白のまま表示されます。 これらの値は、ゲートウェイが実行中の状態になったときに作成されます。

### <a name="configure-an-application-gateway-by-using-xml"></a>XML を使用してアプリケーション ゲートウェイを構成する

次の例では、XML ファイルを使用して、アプリケーション ゲートウェイの設定すべてを構成し、アプリケーション ゲートウェイのリソースにコミットします。  

次のテキストをメモ帳にコピーします。

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

構成項目のかっこに囲まれた値を編集します。 拡張子 .xml のファイルに保存します。

次の例では、パブリック ポート 80 で HTTP トラフィックを負荷分散するアプリケーション ゲートウェイを設定し、カスタム プローブを使用して 2 つの IP アドレスのバックエンド ポート 80 にネットワーク トラフィックを送信する構成ファイルを使用する方法を示します。

> [!IMPORTANT]
> プロトコル項目 HTTP または HTTPS は、大文字小文字を区別します。

カスタム プローブを構成するために、新しい構成項目の \<Probe\> が追加されています。

構成パラメーターは次のとおりです。

|パラメーター|説明|
|---|---|
|**名前** |カスタム プローブの参照名。 |
| **プロトコル** | 使用されるプロトコル (有効な値は HTTP または HTTPS です)。|
| **Host** と **Path** | インスタンスの状態を判断するためにアプリケーション ゲートウェイによって呼び出される完全な URL パス。 たとえば、http:\//contoso.com/ という Web サイトがある場合、HTTP 応答が正常かどうかをプローブでチェックするために、"http:\//contoso.com/path/custompath.htm" に対してカスタム プローブを構成します。|
| **間隔** | プローブのチェック間隔を秒単位で指定します。|
| **タイムアウト** | プローブの HTTP 応答チェックのタイムアウト期間を定義します。|
| **UnhealthyThreshold** | バックエンド インスタンスに "*異常*" というフラグを設定するために必要な HTTP 応答の失敗数。|

プローブの名前は、どのバックエンド プールがカスタム プローブ設定を使用するかを割り当てる \<BackendHttpSettings\> 構成で参照されます。

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>既存の Application Gateway へのカスタム プローブの追加

アプリケーション ゲートウェイの現在の構成を変更するには、現在の XML 構成ファイルの取得、カスタム プローブを追加するための変更、新しい XML 設定を使用したアプリケーション ゲートウェイの構成という 3 つの手順を行う必要があります。

1. `Get-AzureApplicationGatewayConfig` を使用して XML ファイルを取得します。 このコマンドレットにより、プローブ設定を追加するために変更される XML 構成ファイルがエクスポートされます。

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. テキスト エディターで XML ファイルを開きます。 `<frontendport>` の後に `<probe>` セクションを追加します。

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   XML の backendHttpSettings セクションで、次の例に示すようにプローブ名を追加します。

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   XML ファイルを保存します。

1. `Set-AzureApplicationGatewayConfig` を使用して、新しい XML ファイルでアプリケーション ゲートウェイの構成を更新します。 このコマンドレットにより、新しい構成でアプリケーション ゲートウェイが更新されます。

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>次のステップ

トランスポート層セキュリティ (TLS) (旧称 Secure Sockets Layer (SSL)) オフロードを構成する場合は、[TLS オフロードのためのアプリケーション ゲートウェイの構成](./tutorial-ssl-powershell.md)に関するページを参照してください。

内部ロード バランサーと共に使用するようにアプリケーション ゲートウェイを構成する場合は、「 [内部ロード バランサー (ILB) を使用したアプリケーション ゲートウェイの作成](./application-gateway-ilb-arm.md)」を参照してください。