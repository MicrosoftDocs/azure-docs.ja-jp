---
title: 無効なゲートウェイによるエラーのトラブルシューティング - Azure Application Gateway
description: Application Gateway のサーバー エラーをトラブルシューティングする方法について説明します502 - Web サーバーがゲートウェイまたはプロキシ サーバーとして動作しているときに、無効な応答を受信しました。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: d44f9109540c3899ab50bd5c4c02afa19045bafb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182939"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Application Gateway での無効なゲートウェイによるエラーのトラブルシューティング

Azure Application Gateway の使用時に表示される無効なゲートウェイ (502) エラーをトラブルシューティングする方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概要

アプリケーション ゲートウェイの構成後に発生する可能性があるエラーの 1 つに次のようなエラーがあります。"サーバー エラー:502 - Web サーバーがゲートウェイまたはプロキシ サーバーとして動作しているときに、無効な応答を受信しました" というエラーが発生する場合。 このエラーが発生する主な理由としては、次のことが考えられます。

* NSG、UDR、またはカスタム DNS が、バックエンド プールのメンバーへのアクセスをブロックしている。
* バックエンド VM または仮想マシン スケール セットのインスタンスが既定の正常性プローブに応答していない。
* カスタムの正常性プローブの構成が無効または不適切である。
* Azure Application Gateway の[バックエンド プールが構成されていないか、空である](#empty-backendaddresspool)。
* [仮想マシン スケール セット内に正常な](#unhealthy-instances-in-backendaddresspool) VM またはインスタンスがない。
* ユーザー要求で[要求がタイムアウトしたか、接続の問題がある](#request-time-out)。

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>ネットワーク セキュリティ グループ、ユーザー定義ルート、またはカスタム DNS に関する問題

### <a name="cause"></a>原因

NSG、UDR、またはカスタム DNS のためにバックエンドへのアクセスがブロックされている場合、アプリケーション ゲートウェイ インスタンスはバックエンド プールに到達できません。 これが原因でプローブが失敗し、502 エラーが発生します。

NSG/UDR は、アプリケーション ゲートウェイ サブネットまたはアプリケーション VM がデプロイされているサブネットのいずれかに存在する可能性があります。

同様に、VNet にカスタム DNS が存在することが問題の原因になる場合があります。 バックエンド プール メンバーに使用される FQDN が、VNet 用にユーザーが構成した DNS サーバーによって正しく解決されないことがあります。

### <a name="solution"></a>解決策

次の手順によって、NSG、UDR、および DNS の構成を検証します。

* アプリケーション ゲートウェイ サブネットに関連付けられている NSG をチェックします。 バックエンドとの通信がブロックされていないことを確認します。
* アプリケーション ゲートウェイ サブネットに関連付けられている UDR をチェックします。 UDR がトラフィックをバックエンド サブネットから離れるように方向付けていないことを確認します。 たとえば、ネットワーク仮想アプライアンスへのルーティングや、ExpressRoute/VPN 経由でアプリケーション ゲートウェイ サブネットにアドバタイズされる既定のルートをチェックします。

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* VM バックエンドとの有効な NSG と ルートをチェックします。

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* VNet 内のカスタム DNS の存在をチェックします。 DNS は、出力内の VNet プロパティの詳細を調べることでチェックできます。

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
存在する場合は、DNS サーバーがバックエンド プール メンバーの FQDN を正しく解決できることを確認します。

## <a name="problems-with-default-health-probe"></a>既定の正常性プローブに関する問題

### <a name="cause"></a>原因

502 エラーは、既定の正常性プローブがバックエンド VM に到達できないことを示している場合もよくあります。

アプリケーション ゲートウェイ インスタンスがプロビジョニングされると、BackendHttpSetting のプロパティを使用して BackendAddressPool ごとに既定の正常性プローブが自動的に構成されます。 このプローブの設定にはユーザーの入力は必要ありません。 具体的には、負荷分散規則を構成する際に、BackendHttpSetting と BackendAddressPool の間で関連付けが行われます。 既定のプローブがこれらの関連付けごとに構成されます。アプリケーション ゲートウェイは、BackendHttpSetting 要素で指定されたポートで BackendAddressPool 内の各インスタンスに対して定期的な正常性チェック接続を開始します。 

次の表は、既定の正常性プローブに関連する値の一覧です。

| プローブのプロパティ | 値 | 説明 |
| --- | --- | --- |
| プローブの URL |`http://127.0.0.1/` |URL パス |
| Interval |30 |プローブの間隔 (秒) |
| タイムアウト |30 |プローブのタイムアウト (秒) |
| 異常のしきい値 |3 |プローブの再試行回数。 プローブの連続失敗回数が異常のしきい値に達すると、バックエンド サーバーは「ダウン」とマークされます。 |

### <a name="solution"></a>解決策

* 既定のサイトが構成されており、127.0.0.1 でリッスンしていることを確認します。
* BackendHttpSetting で 80 以外のポートが指定されている場合、既定のサイトはポート 80 でリッスンするように構成する必要があります。
* `http://127.0.0.1:port` の呼び出しで、HTTP 結果コード 200 が返されるようにする必要があります。 30 秒のタイムアウト期間内に返されるようにする必要があります。
* 構成済みのポートを開き、構成済みのポートでの送受信トラフィックをブロックするファイアウォール規則または Azure ネットワーク セキュリティ グループが存在しないようにします。
* FQDN またはパブリック IP と共に Azure クラシック VM またはクラウド サービスを使用する場合、対応する[エンドポイント](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=%2fazure%2fapplication-gateway%2ftoc.json)を必ず開いてください。
* Azure Resource Manager を介して VM を構成しており、アプリケーション ゲートウェイがデプロイされた VNet の外側に VM がある場合、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)は、目的のポートにアクセスできるように構成する必要があります。

## <a name="problems-with-custom-health-probe"></a>カスタムの正常性プローブに関する問題

### <a name="cause"></a>原因

カスタムの正常性プローブを使用すれば、既定のプローブ動作の柔軟性を高めることができます。 カスタム プローブを使用する場合、プローブの間隔、テスト対象の URL とパス、バックエンド プール インスタンスを異常とマークするまでの応答の失敗回数を構成することができます。

次のプロパティが追加されます。

| プローブのプロパティ | 説明 |
| --- | --- |
| 名前 |プローブの名前。 この名前は、バックエンドの HTTP 設定でプローブを参照するために使用されます。 |
| Protocol |プローブを送信するために使用するプロトコル。 プローブでは、バックエンドの HTTP 設定で定義されているプロトコルを使用します |
| Host |プローブを送信するホスト名。 アプリケーション ゲートウェイでマルチサイトを構成する場合にのみ適用可能です。 これは VM ホスト名とは異なります。 |
| Path |プローブの相対パス。 パスは、先頭が '/' である必要があります。 プローブは \<protocol\>://\<host\>:\<port\>\<path\> に送信されます |
| Interval |プローブの間隔 (秒)。 2 つの連続するプローブの時間間隔。 |
| タイムアウト |プローブのタイムアウト (秒)。 このタイムアウト期間内に正常な応答が受信されなかった場合は、プローブが「失敗」とマークされます。 |
| 異常のしきい値 |プローブの再試行回数。 プローブの連続失敗回数が異常のしきい値に達すると、バックエンド サーバーは「ダウン」とマークされます。 |

### <a name="solution"></a>解決策

カスタムの正常性プローブが前の表のとおりに正しく構成されていることを確認します。 前のトラブルシューティングの手順を実行したうえで、次のことも必ず行ってください。

* プローブが [ガイド](application-gateway-create-probe-ps.md)のとおりに正しく指定されていることを確認する。
* アプリケーション ゲートウェイを単一のサイトで構成する場合、既定ではホスト名を `127.0.0.1` と指定する必要があります (カスタム プローブで構成する場合は除く)。
* http://\<host\>:\<port\>\<path\> の呼び出しで HTTP 結果コード 200 が返されることを確認する。
* 間隔、タイムアウト、異常のしきい値が許容される範囲内であることを確認する。
* HTTPS プローブを使用する場合は、バックエンド サーバー自体にフォールバック証明書を構成して、バックエンド サーバーが SNI を必要としないようにしてください。

## <a name="request-time-out"></a>要求のタイムアウト

### <a name="cause"></a>原因

ユーザー要求の受信時に、アプリケーション ゲートウェイは構成済みの規則をその要求に適用し、要求をバックエンド プール インスタンスにルーティングします。 Application Gateway は一定時間バックエンド インスタンスからの応答を待ちます。この時間間隔は構成できます。 既定では、この間隔は **20 秒** です。 この間隔の間に アプリケーション ゲートウェイがバックエンド アプリケーションから応答を受信しない場合、ユーザー要求の結果として 502 エラーが表示されます。

### <a name="solution"></a>解決策

Application Gateway では、ユーザーは BackendHttpSetting でこの設定を構成し、別のプールに適用できます。 バックエンド プールごとに異なる BackendHttpSetting を構成できるため、複数の要求のタイムアウトを構成できます。

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>空の BackendAddressPool

### <a name="cause"></a>原因

バックエンド アドレス プールに構成済みの VM または仮想マシン スケール セットがない場合、アプリケーション ゲートウェイでは顧客の要求をルーティングできず、無効なゲートウェイのエラーが送信されます。

### <a name="solution"></a>解決策

バックエンド アドレス プールを空でない状態にしてください。 これには、PowerShell、CLI、ポータルのいずれかを使用できます。

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

前のコマンドレットから取得した出力に、空でないバックエンド アドレス プールが含まれている必要があります。 次の例では、バックエンド VM の FQDN または IP アドレスが構成された 2 つのプールが返されます。 BackendAddressPool のプロビジョニング状態は "Succeeded" である必要があります。

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>BackendAddressPool の異常なインスタンス

### <a name="cause"></a>原因

BackendAddressPool のインスタンスがすべて異常である場合、アプリケーション ゲートウェイにユーザー要求のルーティング先となるバックエンドがありません。 これは、バックエンド インスタンスは正常であるものの、必要なアプリケーションがデプロイされていない場合にも生じることがあります。

### <a name="solution"></a>解決策

インスタンスが正常であり、アプリケーションが正しく構成されていることを確認してください。 バックエンド インスタンスが同じ VNet 内に存在する他の VM からの ping に応答できることをチェックします。 パブリック エンドポイントを構成している場合は、Web アプリケーションに対するブラウザーの要求を処理できるようにします。

## <a name="next-steps"></a>次のステップ

前の手順で問題を解決できない場合は、[サポート チケット](https://azure.microsoft.com/support/options/)を開きます。