---
title: Azure Monitor での Azure リレー メトリック (プレビュー) | Microsoft Docs
description: Azure Monitor を使用した Azure リレーの監視
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: spelluru
ms.openlocfilehash: bd62624406adb006fdcd7d59f72db3fb5e1848a0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798025"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Monitor の Azure リレー メトリック (プレビュー)
Azure リレー メトリックによって、Azure サブスクリプション内のリソースの状態が提供されます。 豊富な一連のメトリック データにより、名前空間レベルだけでなくエンティティ レベルでも、リレー リソースの全体的な正常性を評価できます。 これらの統計は Azure リレーの状態の監視に役立つため、重要になる場合があります。 メトリックはまた、Azure サポートに問い合わせることなく、根本的な問題をトラブルシューティングするのにも役立ちます。

Azure Monitor には、さまざまな Azure サービスにわたって監視するための統合ユーザー インターフェイスが用意されています。 詳細については、「[Microsoft Azure での監視](../monitoring-and-diagnostics/monitoring-overview.md)」および GitHub 上の「[Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)」(.NET を使用した Azure Monitor メトリックの取得) のサンプルを参照してください。

> [!IMPORTANT]
> この記事は WCF リレーではなく、Azure リレーのハイブリッド接続機能にのみ適用されます。 

## <a name="access-metrics"></a>メトリックにアクセスする

Azure Monitor では、複数の方法でメトリックにアクセスできます。 メトリックには [Azure Portal](https://portal.azure.com) 経由でアクセスするか、または Azure Monitor API (REST および .NET) と Operation Management Suite や Event Hubs などの分析ソリューションを使用できます。 詳細については、「[Azure Monitor によって収集された監視データ](../azure-monitor/platform/data-platform.md)」をご覧ください。

メトリックは既定で有効になっており、過去 30 日間のデータにアクセスできます。 データを長期にわたって保持する必要がある場合は、メトリック データを Azure ストレージ アカウントにアーカイブできます。 これは、Azure Monitor の[診断設定](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)で構成されます。

## <a name="access-metrics-in-the-portal"></a>ポータルでメトリックにアクセスする

[Azure Portal](https://portal.azure.com) では、メトリックを時間経過に沿って監視できます。 次の例は、アカウント レベルでの成功した要求と受信要求を表示する方法を示しています。

![][1]

また、名前空間経由でメトリックに直接アクセスすることもできます。 それを行うには、名前空間を選択してから、**[Metrics (Peview)]\(メトリック (プレビュー))** をクリックします。 

ディメンションをサポートするメトリックについては、目的のディメンション値でフィルター処理する必要があります。

## <a name="billing"></a>課金

Azure Monitor でのメトリックの使用は現在、プレビュー段階にある間は無料です。 ただし、メトリック データを取り込む追加のソリューションを使用する場合は、これらのソリューションによって課金される可能性があります。 たとえば、メトリック データを Azure ストレージ アカウントにアーカイブする場合は、Azure Storage によって課金されます。 また、高度な分析のためにメトリック データを Azure Monitor ログにストリーミングする場合は、Azure Monitor ログによっても課金されます。

次のメトリックによって、サービスの正常性の概要が提供されます。 

> [!NOTE]
> いくつかのメトリックは別の名前で移行されているため、非推奨にしています。 これにより、参照の更新が必要になる可能性があります。 "非推奨" というキーワードでマークされているメトリックは、将来サポートされなくなります。

すべてのメトリック値が 1 分ごとに Azure Monitor に送信されます。 時間の粒度は、メトリック値が提供される時間間隔を定義します。 すべての Azure リレー メトリックに対してサポートされる時間間隔は 1 分です。

## <a name="connection-metrics"></a>接続のメトリック

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
| ListenerConnections-Success (プレビュー) | 指定された期間に成功した Azure リレーに対するリスナーの接続数。 <br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ListenerConnections-ClientError (プレビュー)|指定された期間にリスナー接続で発生したクライアント エラー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ListenerConnections-ServerError (プレビュー)|指定された期間にリスナー接続で発生したサーバー エラー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderConnections-Success (プレビュー)|指定された期間に成功した送信側の接続数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderConnections-ClientError (プレビュー)|指定された期間に送信側の接続で発生したクライアント エラー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderConnections-ServerError (プレビュー)|指定された期間に送信側の接続で発生したサーバー エラー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ListenerConnections-TotalRequests (プレビュー)|指定された期間のリスナー接続数の合計。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderConnections-TotalRequests (プレビュー)|指定された期間に送信側が行った接続要求数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ActiveConnections (プレビュー)|指定された期間のアクティブな接続数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ActiveListeners (プレビュー)|指定された期間のアクティブなリスナー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ListenerDisconnects (プレビュー)|指定された期間に切断されたリスナー数。<br/><br/> 単位:Bytes <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderDisconnects (プレビュー)|指定された期間に切断された送信側数。<br/><br/> 単位:Bytes <br/> 集計の種類:合計 <br/> ディメンション:EntityName|

## <a name="memory-usage-metrics"></a>メモリ使用状況のメトリック

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
|BytesTransferred (プレビュー)|指定された期間に転送されたバイト数。<br/><br/> 単位:Bytes <br/> 集計の種類:合計 <br/> ディメンション:EntityName|

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure リレーは、Azure Monitor でのメトリックの次のディメンションをサポートします。 メトリックへのディメンションの追加は省略可能です。 ディメンションを追加しない場合、メトリックは名前空間レベルで指定されます。 

|ディメンション名|説明|
| ------------------- | ----------------- |
|EntityName| Azure リレーは、名前空間の下のメッセージング エンティティをサポートします。|

## <a name="next-steps"></a>次の手順

「[Microsoft Azure での監視の概要](../monitoring-and-diagnostics/monitoring-overview.md)」を参照してください。

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




