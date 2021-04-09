---
title: 診断ログを使用した Azure VPN Gateway のトラブルシューティング
description: 診断ログを使用した Azure VPN Gateway のトラブルシューティング
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772003"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>診断ログを使用した Azure VPN Gateway のトラブルシューティング

この記事では、VPN Gateway 診断で使用できるさまざまなログと、それらを使用して VPN ゲートウェイに関する問題のトラブルシューティングを効果的に行う方法について説明します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Azure では、次のログを使用できます。

|"***名前** | _ *_説明_** |
|---        | ---               |
|**GatewayDiagnosticLog** | ゲートウェイ構成イベント、主要な変更、およびメンテナンス イベントの診断ログが含まれています。 |
|**TunnelDiagnosticLog** | トンネルの状態変更イベントが含まれています。 トンネルの接続/切断イベントには、状態変更の理由の概要があります (該当する場合)。 |
|**RouteDiagnosticLog** | ゲートウェイで発生する静的ルートへの変更および BGP イベントがログに記録されます。 |
|**IKEDiagnosticLog** | ゲートウェイ上の IKE コントロールのメッセージおよびイベントがログに記録されます。 |
|**P2SDiagnosticLog** | ゲートウェイ上のポイント対サイト コントロールのメッセージおよびイベントがログに記録されます。 |

これらのテーブルには使用できる列が複数存在します。 この記事では、ログの使用を簡略化するために、最も関連性の高いものだけを表示しています。

## <a name="set-up-logging"></a><a name="setup"></a>ログを設定する

Azure Log Analytics を使用して Azure VPN Gateway の診断ログ イベントを設定する方法については、[VPN Gateway から診断ログイベントのアラートを設定](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log)する方法に関するページを参照してください。

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

構成の変更は、**GatewayDiagnosticLog** テーブルで監査されます。 実行した変更がログに反映されるまでに、数分かかることがあります。

以下に、参照用のサンプル クエリを示します。

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

**GatewayDiagnosticLog** に対するこのクエリでは、以下の複数の列が表示されます。

|"***名前** | _ *_説明_** |
|---        | ---               |
|**TimeGenerated** | 各イベントのタイムスタンプ (UTC タイムゾーン)。|
|**OperationName** |発生したイベント。 *SetGatewayConfiguration、SetConnectionConfiguration、HostMaintenanceEvent、GatewayTenantPrimaryChanged、MigrateCustomerSubscription、GatewayResourceMove、ValidateGatewayConfiguration* のいずれかになります。|
|**メッセージ** | 実行されている操作の詳細と、成功または失敗した結果の一覧を表示します。|

次の例は、新しい構成が適用されたときに記録されたアクティビティを示しています。

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="GatewayDiagnosticLog に表示される Set Gateway 操作の例。":::


VPN Gateway またはローカル ネットワーク ゲートウェイの両方で構成が変更されるたびに、SetGatewayConfiguration がログに記録されることに注目してください。
**GatewayDiagnosticLog** テーブルの結果と **TunnelDiagnosticLog** テーブルの結果を相互参照すると、構成が変更されたとき、またはメンテナンスが行われたときに、トンネル接続エラーが開始されたかどうかを判断するのに役立ちます。 その場合、考えられる根本原因を見つけ出す良い方法があります。

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

**TunnelDiagnosticLog** テーブルは、トンネルの接続状態の履歴を調べるのに非常に便利です。

以下に、参照用のサンプル クエリを示します。

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

**TunnelDiagnosticLog** に対するこのクエリでは、以下の複数の列が表示されます。


|"***名前** | _ *_説明_** |
|---        | ---               |
|**TimeGenerated** | 各イベントのタイムスタンプ (UTC タイムゾーン)。|
|**OperationName** | 発生したイベント。 *TunnelConnected* または *TunnelDisconnected* のいずれかになります。|
| **Instance\_s** | イベントをトリガーしたゲートウェイ ロール インスタンス。 GatewayTenantWorker\_IN\_0 または GatewayTenantWorker\_IN\_1 のいずれかになります (ゲートウェイの 2 つのインスタンスの名前)。|
| **リソース** | VPN ゲートウェイの名前を示します。 |
| **ResourceGroup** | ゲートウェイが存在するリソース グループを示します。|


出力例:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="TunnelDiagnosticLog に表示されるトンネル接続イベントの例。":::


**TunnelDiagnosticLog** は、予期しない VPN 切断に関する過去のイベントのトラブルシューティングに非常に役立ちます。 情報量が多くないため、あまり労力をかけずに、数日間にわたる長期間の分析を行うこともできます。
切断のタイムスタンプを特定した後に、**IKEdiagnosticLog** テーブルの詳細な分析に切り替えて詳細を調べ、切断の理由が IPsec 関連のものかどうかを判別することができます。


トラブルシューティングに関するヒント:
- 1 つのゲートウェイ インスタンスで切断イベントが発生した後、数秒後に **異なる** ゲートウェイ インスタンスで接続イベントが発生する場合、ゲートウェイのフェールオーバーを調べます。 これは通常、ゲートウェイ インスタンスのメンテナンスのために予期される動作です。 この動作の詳細については、「[AZURE VPN gateway の冗長性について](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy)」を参照してください。
- Azure 側でゲートウェイのリセットを意図的に実行すると (アクティブなゲートウェイ インスタンスが再起動されます)、これと同じ動作が見られます。 この動作の詳細については、[VPN Gateway のリセット](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic)に関するページを参照してください。
- 1 つのゲートウェイ インスタンスで切断イベントが発生した後、数秒後に **同じ** ゲートウェイ インスタンスで接続イベントが発生する場合、DPD タイムアウトを引き起こしているネットワーク障害、またはオンプレミス デバイスによる誤送信によって引きこされた切断がないか調べます。

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

**RouteDiagnosticLog** テーブルは、静的に変更されたルートまたは BGP 経由で受信したルートのアクティビティをトレースします。

以下に、参照用のサンプル クエリを示します。

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

**RouteDiagnosticLog** に対するこのクエリでは、以下の複数の列が表示されます。

|"***名前** | _ *_説明_** |
|---        | ---               |
|**TimeGenerated** | 各イベントのタイムスタンプ (UTC タイムゾーン)。|
|**OperationName** | 発生したイベント。 *StaticRouteUpdate、BgpRouteUpdate、BgpConnectedEvent、BgpDisconnectedEvent* のいずれかになります。|
| **メッセージ** | 実行されている操作の詳細。|

出力には、接続/切断された BGP ピアと、交換されたルートに関する有用な情報が表示されます。

例:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="RouteDiagnosticLog に表示される BGP ルート交換アクティビティの例。":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

**IKEDiagnosticLog** テーブルは、IKE/IPsec の詳細なデバッグ ログを提供します。 これは、切断のトラブルシューティングを行う場合や、VPN シナリオに接続できない場合に役立ちます。

以下に、参照用のサンプル クエリを示します。

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

**IKEDiagnosticLog** に対するこのクエリでは、以下の複数の列が表示されます。


|"***名前** | _ *_説明_** |
|---        | ---               |
|**TimeGenerated** | 各イベントのタイムスタンプ (UTC タイムゾーン)。|
| **RemoteIP** | オンプレミス VPN デバイスの IP アドレス。 実際のシナリオでは、複数存在する場合に、関連するオンプレミス VPN デバイスの IP アドレスでフィルター処理できると便利です。 |
|**LocalIP** | トラブルシューティングを行う VPN Gateway の IP アドレス。 実際のシナリオでは、サブスクリプションに複数存在する場合に、関連する VPN Gateway の IP アドレスでフィルター処理できると便利です。 |
|**Event** | トラブルシューティングに役立つ診断メッセージが含まれています。 通常、キーワードで始まり、Azure ゲートウェイによって実行されるアクションを参照します。 **\[SEND\]** は、Azure ゲートウェイによって送信された IPSec パケットによって発生したイベントを示します。  **\[RECEIVED\]** は、オンプレミスのデバイスから受信したパケットによって発生したイベントを示します。  **\[LOCAL\]** は、Azure ゲートウェイによってローカルで実行されるアクションを示します。 |


RemoteIP、LocalIP、および Event 列が AzureDiagnostics データベースの元の列リストに存在していませんが、分析を簡略化するために、"Message" 列の出力を解析することによって、クエリに追加されている点に注意してください。

トラブルシューティングのヒント:

- IPSec ネゴシエーションの開始を識別するには、最初の SA\_INIT メッセージを見つける必要があります。 このようなメッセージは、トンネルのどちらの側からも送信される可能性があります。 IPsec 用語では、最初のパケットを送信する側が "イニシエーター" と呼ばれ、もう一方は "レスポンダー" と呼ばれます。 最初の SA\_INIT メッセージは、常に rCookie = 0 になります。

- IPsec トンネルの確立に失敗した場合、Azure は数秒ごとに再試行を続けます。 このため、問題を再現するために一定時間待つ必要がないため、IKEdiagnosticLog で "VPN ダウン" の問題のトラブルシューティングを行うのが便利です。 また、この失敗は理論上、試行するたびに常に同じ結果になるため、失敗する 1 つの "サンプル" ネゴシエーションに絞って調べることができます。

- SA\_INIT には、ピアがこの IPSec ネゴシエーションに使用する IPSec パラメーターが含まれます。 公式ドキュメント   
「[既定の IPSec/IKE パラメーター](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec)」には、既定の設定で Azure ゲートウェイでサポートされる IPSec パラメーターが示されています。


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

VPN 診断で使用可能な最後のテーブルは **P2SDiagnosticLog** です。 このテーブルは、ポイント対サイトのアクティビティをトレースします。

以下に、参照用のサンプル クエリを示します。

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

**P2SDiagnosticLog** に対するこのクエリでは、以下の複数の列が表示されます。

|"***名前** | _ *_説明_** |
|---        | ---               |
|**TimeGenerated** | 各イベントのタイムスタンプ (UTC タイムゾーン)。|
|**OperationName** | 発生したイベント。 *P2SLogEvent* になります。|
| **メッセージ** | 実行されている操作の詳細。|

出力には、ゲートウェイが適用したポイント対サイトの設定、および IPsec ポリシーがすべて表示されます。

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="P2SDiagnosticLog に表示されるポイント対サイト接続の例。":::

また、クライアントが IKEv2 または OpenVPN ポイント対サイト経由で接続するたびに、このテーブルはパケット アクティビティ、EAP/RADIUS 通信、およびユーザーによる成功/失敗の結果をログに記録します。

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="P2SDiagnosticLog に表示される EAP 認証の例。":::

## <a name="next-steps"></a>次の手順

トンネル リソース ログにアラートを構成するには、[VPN Gateway リソース ログのアラートの設定](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)に関するページを参照してください。

