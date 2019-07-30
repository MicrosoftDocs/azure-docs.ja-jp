---
title: Azure でロード バランサーの TCP アイドル タイムアウトを構成する
titlesuffix: Azure Load Balancer
description: ロード バランサーの TCP アイドル タイムアウトの構成
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: b3df1ead7a3164ffd9a4b4acf8820d0f5b82cee3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274180"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Azure Load Balancer の TCP アイドル タイムアウト設定を構成する

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

既定の構成では、Azure Load Balancer はアイドル タイムアウトが 4 分に設定されています。 アイドル時間がタイムアウト値よりも長い場合、クライアントとクラウド サービス間の TCP または HTTP セッションが維持されるという保証はありません。

接続が閉じられると、クライアント アプリケーションは、次のエラー メッセージを受信する場合があります。"基になる接続が閉じられました: 維持される必要があった接続が、サーバーによって切断されました。"

一般的な方法として、TCP keep-alive を使用します。 この方法を使用すると、接続が長時間アクティブ状態に維持されます。 詳細については、こちらの [.NET の例](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)をご覧ください。 keep-alive を有効にすると、接続のアイドル時間にパケットが送信されます。 これらの keep-alive パケットにより、アイドル タイムアウト値に達することがなくなり、接続を長時間維持できるようになります。

この設定は着信接続のみ使用できます。 接続の切断を避けるためには、アイドル タイムアウト設定よりも小さい間隔で、TCP keep-alive を構成するか、アイドル タイムアウト値を大きくします。 このようなシナリオをサポートするために追加されたのが、構成可能なアイドル タイムアウトです。 これにより、タイムアウトを 4 ～ 30 分に設定できるようになりました。

TCP keep-alive は、バッテリーの寿命に制約がないシナリオに適しています。 モバイル アプリケーションでは推奨されません。 モバイル アプリケーションで TCP keep-alive を使用すると、デバイスのバッテリーの消耗を速める可能性があります。

![TCP タイムアウト](./media/load-balancer-tcp-idle-timeout/image1.png)

次のセクションでは、仮想マシンとクラウド サービスのアイドル タイムアウト設定を変更する方法について説明します。

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>インスタンスレベル パブリック IP の TCP タイムアウトを 15 分で構成します。

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` はオプションです。 これを設定しない場合、既定のタイムアウトは 4 分です。 設定できるタイムアウトの範囲は 4 ～ 30 分です。

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>仮想マシンでの Azure エンドポイントの作成時にアイドル タイムアウトを設定する

エンドポイントのタイムアウト設定を変更するには、次のコマンドを使用します。

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

アイドル タイムアウト構成を取得するには、次のコマンドを使用します。

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>負荷分散エンドポイント セットでの TCP タイムアウトを設定する

エンドポイントが負荷分散エンドポイント セットの一部である場合、その負荷分散エンドポイント セットで TCP タイムアウトを設定する必要があります。 例:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>クラウド サービスのタイムアウト設定を変更する

Azure SDK を使用してクラウド サービスを更新できます。 クラウド サービスのエンドポイントの設定は、.csdef ファイルで行います。 クラウド サービスを展開するための TCP タイムアウトを更新するには、デプロイのアップグレードが必要です。 TCP タイムアウトがパブリック IP 向けにのみ指定されている場合は例外として扱われます。 パブリック IP は .cscfg ファイルで設定され、デプロイのアップデートやアップグレードを通じて更新することができます。

エンドポイント設定の .csdef の変更は次のように行います。

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

パブリック IP のタイムアウト設定の .cscfg の変更は次のように行います。

```xml
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
    </InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="rest-api-example"></a>Rest API の例

TCP アイドルのタイムアウトは、サービス管理 API を使って構成できます。 `x-ms-version` ヘッダーが、`2014-06-01` 以降のバージョンに設定されていることを確認します。 デプロイされているすべての仮想マシンで、指定した負荷分散入力エンドポイントの構成をアップデートします。

### <a name="request"></a>Request

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Response

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>次の手順

[内部ロード バランサーの概要](load-balancer-internal-overview.md)

[インターネットに接続するロード バランサーの構成の開始](load-balancer-get-started-internet-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)
