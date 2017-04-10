---
title: "ロード バランサー分散モードの構成 |Microsoft Docs"
description: "ソース IP アフィニティをサポートするように Azure Load Balancer の分散モードを構成する方法"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 664e668d6660fae956d4b6bdf0179abf67d743b1
ms.openlocfilehash: 1bc28b6f2979f47284a27a5a25becb8599b2ffab

---

# <a name="configure-the-distribution-mode-for-load-balancer"></a>Load Balancer の分散モードを構成する

## <a name="hash-based-distribution-mode"></a>ハッシュベースの分散モード

既定の分散アルゴリズムは、使用可能なサーバーにトラフィックをマップする 5 タプル (ソース IP、ソース ポート、接続先 IP、接続先ポート、プロトコルの種類) ハッシュです。 これは、トランスポート セッション内でのみ持続性を提供します。 同じセッション内のパケットは、負荷分散されたエンドポイントの背後にある同じデータ センター IP (DIP) インスタンスに送信されます。 クライアントが同じソース IP から新しいセッションを開始すると、ソース ポートが変更され、トラフィックは別の DIP エンドポイントに送信されます。

![ハッシュ ベースのロード バランサー](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

図 1 - 5 タプル分散

## <a name="source-ip-affinity-mode"></a>ソース IP アフィニティ モード

ソース IP アフィニティと呼ばれる別の分散モードがあります (セッション アフィニティまたはクライアント IP アフィニティとも呼ばれます)。 2 タプル (ソース IP、接続先 IP) または 3 タプル (ソース IP、接続先 IP、プロトコル) を使用して使用可能なサーバーにトラフィックをマップするように Azure Load Balancer を構成できます。 ソース IP アフィニティを使用して、同じクライアント コンピューターから開始された接続は、同じ DIP エンドポイントに移動します。

次の図は 2 タプル構成を示しています。 2 タプルがロード バランサーから仮想マシン 1 (VM1) に流れた後、VM2 と VM3 でバックアップされます。

![セッション アフィニティ](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

図 2 - 2 タプル分散

ソース IP アフィニティは、Azure Load Balancer とリモート デスクトップ (RD) ゲートウェイ間の非互換性の問題を解決します。 これで、1 つのクラウド サービス内に RD ゲートウェイ ファームを構築できます。

もう 1 つの使用シナリオは、データのアップロードを UDP 経由で行い、コントロール プレーンは TCP 経由で実現するメディア アップロードです。

* クライアントは、まず負荷分散されたパブリック アドレスに TCP セッションを開始し、特定の DIP に接続します。このチャネルは、アクティブなまま接続の状態を監視します。
* 同じクライアント コンピューターからの新しい UDP セッションが、同じ負荷分散されたパブリック エンドポイントに開始されます。この接続は、前の TCP 接続と同じ DIP エンドポイントにも送られるため、メディアのアップロードは TCP 経由のコントロール チャネルを維持しながら高スループットで実行されることが予想されます。

> [!NOTE]
> (仮想マシンの削除や追加により) 負荷分散されたセットが変更されると、クライアント要求の分散が再計算されます。 最終的に同じサーバーに接続する既存のクライアントからの新しい接続に依存することはできません。 また、ソース IP アフィニティ分散モード を使用すると、トラフィックが均等に分散されない可能性があります。 プロキシの背後で実行しているクライアントは、1 つの固有のクライアント アプリケーションと見なすことができます。

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>ロード バランサーのソース IP アフィニティ設定の構成

仮想マシンの場合、PowerShell を使用してタイムアウト設定を変更できます。

Azure エンドポイントを仮想マシンに追加してロード バランサー分散モードを設定する

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

LoadBalancerDistribution は、2 タプル (ソース IP と接続先 IP) の負荷分散では sourceIP、3 タプル (ソース IP、接続先 IP、プロトコル) の負荷分散では sourceIPProtocol、既定の動作 (5 タプルの負荷分散) を使用する場合は none に設定できます。

エンドポイント ロード バランサー分散モード構成を取得するには、次のコードを使用します。

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

LoadBalancerDistribution 要素が存在しない場合、Azure Load Balancer は既定の 5 組のアルゴリズムを使用します。

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>負荷分散エンドポイント セットで分散モードを設定する

エンドポイントが負荷分散エンドポイント セットの一部である場合、分散モードは負荷分散エンドポイント セットで設定される必要があります。

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>分散モードを変更するクラウド サービス構成

Azure SDK for .NET 2.5 (11 月にリリース予定) を使用してクラウド サービスを更新できます。 クラウド サービスのエンドポイントの設定は、.csdef で行われます。 ロード バランサー分散モードをアップデートしてクラウド サービスをデプロイするには、デプロイのアップグレードが必要です。
エンドポイント設定の .csdef の変更例は次の通りです。

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
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

## <a name="api-example"></a>API の例

ロード バランサーの分散は、サービス管理 API を使用して構成できます。 `2014-09-01` 以降のバージョンに設定された `x-ms-version` ヘッダーを必ず追加します。

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>デプロイで指定した負荷分散セットの構成を更新します。

#### <a name="request-example"></a>要求の例

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

LoadBalancerDistribution の値は、sourceIP (2 タプル アフィニティ)、sourceIPProtocol (3 タプル アフィニティ)、または none (アフィニティなし、 つまり 5 タプル) に設定できます。

#### <a name="response"></a>応答

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>次のステップ

[内部ロード バランサーの概要](load-balancer-internal-overview.md)

[インターネットに接続するロード バランサーの構成の開始](load-balancer-get-started-internet-arm-ps.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO3-->


