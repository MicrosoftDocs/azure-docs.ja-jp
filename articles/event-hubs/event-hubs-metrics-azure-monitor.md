---
title: Azure Monitor でのメトリック - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Monitoring を使用して Azure Event Hubs を監視する方法について説明します。
ms.topic: article
ms.date: 02/25/2021
ms.openlocfilehash: 52ab66fe264b85be117eb8e2e21cb89f38d0fcae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715583"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Monitor での Azure Event Hubs メトリック

Event Hubs メトリックによって、Azure サブスクリプション内の Event Hubs リソースの状態が提供されます。 豊富な一連のメトリック データにより、名前空間レベルだけでなくエンティティ レベルでも、イベント ハブの全体的な正常性を評価できます。 これらの統計はイベント ハブの状態の監視に役立つため、重要になる場合があります。 メトリックはまた、Azure サポートに問い合わせることなく、根本的な問題をトラブルシューティングするのにも役立ちます。

Azure Monitor には、さまざまな Azure サービスにわたって監視するための統合ユーザー インターフェイスが用意されています。 詳細については、「[Microsoft Azure での監視](../azure-monitor/overview.md)」および GitHub 上の「[Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)」(.NET を使用した Azure Monitor メトリックの取得) のサンプルを参照してください。

## <a name="access-metrics"></a>メトリックにアクセスする

Azure Monitor では、複数の方法でメトリックにアクセスできます。 メトリックには [Azure Portal](https://portal.azure.com) 経由でアクセスするか、または Azure Monitor API (REST および .NET) と Log Analytics や Event Hubs などの分析ソリューションを使用できます。 詳細については、「[Azure Monitor によって収集された監視データ](../azure-monitor/data-platform.md)」をご覧ください。

メトリックは既定で有効になっており、過去 30 日間のデータにアクセスできます。 データを長期にわたって保持する必要がある場合は、メトリック データを Azure ストレージ アカウントにアーカイブできます。 この設定は、Azure Monitor の[診断設定](../azure-monitor/essentials/diagnostic-settings.md)で構成できます。


## <a name="access-metrics-in-the-portal"></a>ポータルでメトリックにアクセスする

[Azure Portal](https://portal.azure.com) では、メトリックを時間経過に沿って監視できます。 次の例は、アカウント レベルでの成功した要求と受信要求を表示する方法を示しています。

![正常なメトリックを表示する][1]

また、名前空間経由でメトリックに直接アクセスすることもできます。 それを行うには、名前空間を選択してから、 **[メトリック]** を選択します。 イベント ハブのスコープにフィルター処理されたメトリックを表示するには、イベント ハブを選択してから、 **[メトリック]** を選択します。

ディメンションをサポートするメトリックの場合は、次の例に示すように、目的のディメンション値でフィルター処理する必要があります。

![ディメンションの値でフィルター処理する][2]

## <a name="billing"></a>課金

Azure Monitor でのメトリックの使用は現在無料です。 ただし、メトリック データを取り込む他のソリューションを使用する場合は、これらのソリューションによって課金される可能性があります。 たとえば、メトリック データを Azure ストレージ アカウントにアーカイブする場合は、Azure Storage によって課金されます。 また、高度な分析のためにメトリック データを Azure Monitor ログにストリーム配信する場合は、Azure によっても課金されます。

次のメトリックによって、サービスの正常性の概要が提供されます。 

> [!NOTE]
> いくつかのメトリックは別の名前で移行されているため、非推奨にしています。 これにより、参照の更新が必要になる可能性があります。 "非推奨" というキーワードでマークされているメトリックは、将来サポートされなくなります。

すべてのメトリック値が 1 分ごとに Azure Monitor に送信されます。 時間の粒度は、メトリック値が提供される時間間隔を定義します。 すべての Event Hubs メトリックに対してサポートされる時間間隔は 1 分です。

## <a name="azure-event-hubs-metrics"></a>Azure Event Hubs メトリック
サービスでサポートされているメトリックの一覧については、[Azure Event Hubs](../azure-monitor/essentials/metrics-supported.md#microsofteventhubnamespaces)に関するセクションを参照してください

> [!NOTE]
> ユーザー エラーが発生すると、Azure Event Hubs によって **ユーザー エラー** メトリックは更新されますが、他の診断情報はログに記録されません。 そのため、アプリケーションのユーザー エラーに関する詳細情報を取り込む必要があります。 または、メッセージが送信または受信されたとき生成されるテレメトリをアプリケーション分析情報に変換することもできます。 例については、「[Application Insights を使用した追跡](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights)」を参照してください。

## <a name="azure-monitor-integration-with-siem-tools"></a>SIEM ツールとの Azure Monitor 統合
Azure Monitor を使用して監視データ (アクティビティ ログ、診断ログなど) をイベント ハブにルーティングすると、セキュリティ情報イベント管理 (SIEM) ツールと簡単に統合できます。 詳細については、次の記事/ブログ投稿を参照してください。

- [外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
- [Azure Log Integration の概要](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Azure Monitor を使用して SIEM ツールと統合する](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

SIEM ツールでイベント ハブからログ データを使用するシナリオでは、受信メッセージが表示されない場合や、受信メッセージは表示されものの、メトリック グラフに送信メッセージが表示されない場合、次の手順を実行してください。

- **受信メッセージがない** 場合は、Azure Monitor サービスが監査と診断ログをイベント ハブに移動していないことを意味します。 その場合は、Azure Monitor チームとのサポート チケットを開いてください。 
- 受信メッセージはあるが **送信メッセージがない** 場合は、SIEM アプリケーションがメッセージを読み取っていないことを意味します。 SIEM プロバイダーに問い合わせて、それらのアプリケーションのイベント ハブの構成が正しいかどうかを確認してください。


## <a name="next-steps"></a>次のステップ

* 「[Microsoft Azure での監視の概要](../azure-monitor/overview.md)」を参照してください。
* GitHub 上の「[Retrieve Azure Monitor metrics with .NET (.NET を使用した Azure Monitor メトリックの取得)](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)」のサンプル。 

Event Hubs の詳細については、次のリンクを参照してください。

- Event Hubs のチュートリアルを開始する
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs の FAQ](event-hubs-faq.md)
* [Event Hubs を使用するサンプル アプリケーション](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
