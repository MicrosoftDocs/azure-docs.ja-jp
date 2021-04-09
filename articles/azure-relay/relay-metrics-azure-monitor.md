---
title: Azure Monitor での Azure リレー メトリック | Microsoft Docs
description: この記事では、Azure Monitor を使用して Azure Relay の状態を監視する方法について説明します。
services: service-bus-relay
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7f91ac0aec46d0fc24901585156dceabb18640c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586822"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Monitor の Azure リレー メトリック 
Azure リレー メトリックによって、Azure サブスクリプション内のリソースの状態が提供されます。 豊富な一連のメトリック データにより、名前空間レベルだけでなくエンティティ レベルでも、リレー リソースの全体的な正常性を評価できます。 これらの統計は Azure リレーの状態の監視に役立つため、重要になる場合があります。 メトリックはまた、Azure サポートに問い合わせることなく、根本的な問題をトラブルシューティングするのにも役立ちます。

Azure Monitor には、さまざまな Azure サービスにわたって監視するための統合ユーザー インターフェイスが用意されています。 詳細については、「[Microsoft Azure での監視](../azure-monitor/overview.md)」および GitHub 上の「[Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)」(.NET を使用した Azure Monitor メトリックの取得) のサンプルを参照してください。

> [!IMPORTANT]
> この記事は WCF リレーではなく、Azure リレーのハイブリッド接続機能にのみ適用されます。 

## <a name="access-metrics"></a>メトリックにアクセスする

Azure Monitor では、複数の方法でメトリックにアクセスできます。 メトリックには [Azure Portal](https://portal.azure.com) 経由でアクセスするか、または Azure Monitor API (REST および .NET) と Operation Management Suite や Event Hubs などの分析ソリューションを使用できます。 詳細については、「[Azure Monitor によって収集された監視データ](../azure-monitor/data-platform.md)」をご覧ください。

メトリックは既定で有効になっており、過去 30 日間のデータにアクセスできます。 データを長期にわたって保持する必要がある場合は、メトリック データを Azure ストレージ アカウントにアーカイブできます。 これは、Azure Monitor の[診断設定](../azure-monitor/essentials/diagnostic-settings.md)で構成されます。

## <a name="access-metrics-in-the-portal"></a>ポータルでメトリックにアクセスする

[Azure Portal](https://portal.azure.com) では、メトリックを時間経過に沿って監視できます。 次の例は、アカウント レベルでの成功した要求と受信要求を表示する方法を示しています。

!["[モニター] - [メトリック] (プレビュー)" というタイトルのページに、過去 30 日間のメモリ使用量の折れ線グラフが表示されます。][1]

また、名前空間経由でメトリックに直接アクセスすることもできます。 それを行うには、名前空間を選択してから、 **[Metrics]\(メトリック\)** をクリックします。 

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
| ListenerConnections-Success  | 指定された期間に成功した Azure リレーに対するリスナーの接続数。 <br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ListenerConnections-ClientError |指定された期間にリスナー接続で発生したクライアント エラー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ListenerConnections-ServerError |指定された期間にリスナー接続で発生したサーバー エラー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderConnections-Success |指定された期間に成功した送信側の接続数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderConnections-ClientError |指定された期間に送信側の接続で発生したクライアント エラー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderConnections-ServerError |指定された期間に送信側の接続で発生したサーバー エラー数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ListenerConnections-TotalRequests |指定された期間のリスナー接続数の合計。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderConnections-TotalRequests |指定された期間に送信側が行った接続要求数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ActiveConnections |アクティブな接続の数。 この値は、特定の時点の値です。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ActiveListeners |アクティブなリスナーの数。 この値は、特定の時点の値です。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ListenerDisconnects |指定された期間に切断されたリスナー数。<br/><br/> 単位:バイト <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|SenderDisconnects |指定された期間に切断された送信側数。<br/><br/> 単位:バイト <br/> 集計の種類:合計 <br/> ディメンション:EntityName|

## <a name="memory-usage-metrics"></a>メモリ使用状況のメトリック

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
|BytesTransferred |指定された期間に転送されたバイト数。<br/><br/> 単位:バイト <br/> 集計の種類:合計 <br/> ディメンション:EntityName|

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure リレーは、Azure Monitor でのメトリックの次のディメンションをサポートします。 メトリックへのディメンションの追加は省略可能です。 ディメンションを追加しない場合、メトリックは名前空間レベルで指定されます。 

|ディメンション名|説明|
| ------------------- | ----------------- |
|EntityName| Azure リレーは、名前空間の下のメッセージング エンティティをサポートします。|

## <a name="next-steps"></a>次のステップ

「[Microsoft Azure での監視の概要](../azure-monitor/overview.md)」を参照してください。

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png
