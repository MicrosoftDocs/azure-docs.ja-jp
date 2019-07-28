---
title: Azure Network Watcher のリソース トラブルシューティングの概要 | Microsoft Docs
description: このページでは、Network Watcher のリソース トラブルシューティング機能の概要を説明します。
services: network-watcher
documentationcenter: na
author: kumudd
manager: twooley
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 65ce9e7d298131486ae4e5f3584c7975ca81e1ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684239"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Azure Network Watcher のリソース トラブルシューティングの概要

仮想ネットワーク ゲートウェイにより、オンプレミスのリソースと Azure 内の別の仮想ネットワークを接続できます。 ゲートウェイやその接続の監視は、通信が切断されていないことを確認するために重要です。 Network Watcher には、ゲートウェイや接続をトラブルシューティングする機能が用意されています。 この機能は、ポータル、PowerShell、Azure CLI、または REST API から呼び出すことができます。 呼び出されると、Network Watcher はゲートウェイまたは接続の正常性を診断し、該当する結果を返します。 この要求は、実行時間の長いトランザクションです。 診断が完了すると、結果が返されます。

![ポータル][2]

## <a name="results"></a>結果

返される暫定結果では、リソースの正常性の概要が示されます。 次のセクションに示すように、リソースに関するより詳細な情報を得ることもできます。

次の一覧に、このトラブルシューティング API で返される値を示します。

* **startTime** - この値は、トラブルシューティング API の呼び出しが開始された時刻を示します。
* **endTime** - この値は、トラブルシューティングが終了した時刻を示します。
* **code** - この値は、診断エラーが 1 つある場合 UnHealthy になります。
* **results** - 結果は、対象の接続または仮想ネットワーク ゲートウェイについて返された各結果をまとめたものです。
    * **id** - この値は、エラーの種類を示します。
    * **summary** - この値は、エラーの概要を示します。
    * **detailed** - この値は、エラーの詳細な説明を示します。
    * **recommendedActions** - このプロパティは、実施すべき推奨対策をまとめたものです。
      * **actionText** - この値には、実施すべき対策について説明するテキストが含まれます。
      * **actionUri** - この値には、実施方法に関するドキュメントへの URI が示されます。
      * **actionUriText** - この値は、対策に関するテキストの簡単な説明が示されます。

次の表に、起こり得るさまざまなエラーの種類 (id は前述の一覧の results のもの) とエラーによりログが作成されるかどうかを示します。

### <a name="gateway"></a>Gateway

| エラーの種類 | 理由 | ログ|
|---|---|---|
| NoFault | エラーが検出されなかった場合 |はい|
| GatewayNotFound | ゲートウェイが見つからないか、またはゲートウェイがプロビジョニングされていません。 |いいえ|
| PlannedMaintenance |  ゲートウェイ インスタンスがメンテナンス中です。  |いいえ|
| UserDrivenUpdate | このエラーは、ユーザーの更新が進行中である場合に発生します。 この更新は、サイズ変更操作である可能性があります。 | いいえ |
| VipUnResponsive | このエラーは、正常性プローブの失敗のためにゲートウェイのプライマリ インスタンスに到達できない場合に発生します。 | いいえ |
| PlatformInActive | プラットフォームに問題があります。 | いいえ|
| ServiceNotRunning | 基になるサービスが実行されていません。 | いいえ|
| NoConnectionsFoundForGateway | ゲートウェイ上に接続が存在しません。 このエラーは、単なる警告です。| いいえ|
| ConnectionsNotConnected | どの接続も接続されていません。 このエラーは、単なる警告です。| はい|
| GatewayCPUUsageExceeded | 現在のゲートウェイの CPU 使用率が 95% を超えています。 | はい |

### <a name="connection"></a>接続

| エラーの種類 | 理由 | ログ|
|---|---|---|
| NoFault | エラーが検出されなかった場合 |はい|
| GatewayNotFound | ゲートウェイが見つからないか、またはゲートウェイがプロビジョニングされていません。 |いいえ|
| PlannedMaintenance | ゲートウェイ インスタンスがメンテナンス中です。  |いいえ|
| UserDrivenUpdate | このエラーは、ユーザーの更新が進行中である場合に発生します。 この更新は、サイズ変更操作である可能性があります。  | いいえ |
| VipUnResponsive | このエラーは、正常性プローブの失敗のためにゲートウェイのプライマリ インスタンスに到達できない場合に発生します。 | いいえ |
| ConnectionEntityNotFound | 接続の構成がありません。 | いいえ |
| ConnectionIsMarkedDisconnected | 接続が "切断" とマークされています。 |いいえ|
| ConnectionNotConfiguredOnGateway | 基になるサービスの接続が構成されていません。 | はい |
| ConnectionMarkedStandby | 基になるサービスがスタンバイとマークされています。| はい|
| Authentication | 事前共有キーが一致しません。 | はい|
| PeerReachability | ピア ゲートウェイに到達できません。 | はい|
| IkePolicyMismatch | ピア ゲートウェイに、Azure のサポート対象外の IKE ポリシーが設定されています。 | はい|
| WfpParse Error | WFP ログの解析中にエラーが発生しました。 |はい|

## <a name="supported-gateway-types"></a>サポートされるゲートウェイの種類

次の表は、Network Watcher のトラブルシューティングでサポートされるゲートウェイと接続の一覧を示しています。

|  |  |
|---------|---------|
|**ゲートウェイの種類**   |         |
|VPN      | サポートされています        |
|ExpressRoute | サポートされていません |
|**VPN の種類** | |
|ルート ベース | サポートされています|
|ポリシー ベース | サポートされていません|
|**接続の種類**||
|IPSec| サポートされています|
|VNet2Vnet| サポートされています|
|ExpressRoute| サポートされていません|
|VPNClient| サポートされていません|

## <a name="log-files"></a>ログ ファイル

リソース トラブルシューティングが完了すると、そのログ ファイルがストレージ アカウント内に保存されます。 次の画像に、エラーが検出された呼び出しのログ ファイルの例を示します。

![zip ファイル][1]

> [!NOTE]
> 場合によっては、一部のログ ファイルのみがストレージに書き込まれることがあります。

Azure ストレージ アカウントからファイルをダウンロードする方法については、「[.NET を使用して Azure Blob Storage を使用する](../storage/blobs/storage-dotnet-how-to-use-blobs.md)」を参照してください。 使用できるツールとして他に Storage Explorer があります。 ストレージ エクスプローラーの詳細については、次のリンクを参照してください。[Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

**ConnectionStats.txt** ファイルには、送受信バイト数、接続の状態、接続の確立時刻など、接続の全統計情報が含まれます。

> [!NOTE]
> トラブルシューティング API への呼び出し結果が正常であった場合、zip ファイルで返されるものは **ConnectionStats.txt** ファイルのみです。

このファイルの内容は次の例のようになります。

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**CPUStats.txt** ファイルには、テスト時の CPU 使用率および使用可能メモリが記載されています。  このファイルの内容は次の例のようになります。

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt** ファイルには、監視中に見つかった IKE に関するエラーが含まれます。

次の例に、IKEErrors.txt ファイルの内容を示します。 問題によって、エラーは異なる場合があります。

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

**Scrubbed-wfpdiag.txt** ログ ファイルには、wfp のログが記載されます。 このログには、パケット ドロップと IKE/AuthIP のエラーが記録されます。

次の例に、Scrubbed-wfpdiag.txt ファイルの内容を示します。 この例では、下から 3 行目でわかるように、接続の共有キーが正しくありませんでした。 問題に応じてログが非常に長くなることがあるため、次の例ではログ全体の一部のみを示しています。

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

**wfpdiag.txt.sum** ファイルは、処理されたバッファーおよびプロセスを示すログです。

次の例に、wfpdiag.txt.sum ファイルの内容を示します。
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>次の手順

ゲートウェイまたはゲートウェイの接続に関する問題を診断する方法については、[ネットワーク間の通信の問題を診断する方法](diagnose-communication-problem-between-networks.md)に関するページを参照してください。
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
