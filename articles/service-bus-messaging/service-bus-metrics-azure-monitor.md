---
title: Azure Monitor での Azure Service Bus メトリック | Microsoft Docs
description: Azure Monitor を使用して Service Bus エンティティを監視する
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: 80a4b1e60202b88f6ed3c1574bd4684575a9b153
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538072"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Monitor での Azure Service Bus メトリック

Service Bus メトリックによって、Azure サブスクリプション内のリソースの状態が提供されます。 豊富な一連のメトリック データにより、名前空間レベルだけでなくエンティティ レベルでも、Service Bus リソースの全体的な正常性を評価できます。 これらの統計は Service Bus の状態の監視に役立つため、重要になる場合があります。 メトリックはまた、Azure サポートに問い合わせることなく、根本的な問題をトラブルシューティングするのにも役立ちます。

Azure Monitor には、さまざまな Azure サービスにわたって監視するための統合ユーザー インターフェイスが用意されています。 詳細については、「[Microsoft Azure での監視](../monitoring-and-diagnostics/monitoring-overview.md)」および GitHub 上の「[Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api)」(.NET を使用した Azure Monitor メトリックの取得) のサンプルを参照してください。

> [!IMPORTANT]
> エンティティとの間で相互作用が 2 時間ない場合、エンティティがアイドル状態でなくなるまで、メトリックは値として "0" を示すようになります。

## <a name="access-metrics"></a>メトリックにアクセスする

Azure Monitor では、複数の方法でメトリックにアクセスできます。 メトリックには [Azure portal](https://portal.azure.com) 経由でアクセスするか、または Azure Monitor API (REST および .NET) と Azure Monitor ログや Event Hubs などの分析ソリューションを使用してアクセスできます。 詳しくは、「[Metrics in Azure Monitor (Azure Monitor のメトリック)](../azure-monitor/platform/data-platform-metrics.md)」をご覧ください。

メトリックは既定で有効になっており、過去 30 日間のデータにアクセスできます。 データを長期にわたって保持する必要がある場合は、メトリック データを Azure ストレージ アカウントにアーカイブできます。 この値は、Azure Monitor の[診断設定](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)で構成します。

## <a name="access-metrics-in-the-portal"></a>ポータルでメトリックにアクセスする

[Azure Portal](https://portal.azure.com) では、メトリックを時間経過に沿って監視できます。 次の例は、アカウント レベルでの成功した要求と受信要求を表示する方法を示しています。

![][1]

また、名前空間経由でメトリックに直接アクセスすることもできます。 それを行うには、名前空間を選択してから、 **[Metrics]\(メトリック\)** をクリックします。 エンティティのスコープにフィルター処理されたメトリックを表示するには、エンティティを選択し、 **[メトリック]** をクリックします。

![][2]

ディメンションをサポートするメトリックについては、目的のディメンション値でフィルター処理する必要があります。

## <a name="billing"></a>課金

Azure Monitor のメトリックとアラートは、アラート単位で課金されます。 これらの料金は、アラートが設定されたときと、それが保存される前にポータルで確認できます。 

メトリック データを取り込む追加のソリューションは、それらのソリューションによって直接課金されます。 たとえば、メトリック データを Azure ストレージ アカウントにアーカイブする場合は、Azure Storage によって課金されます。 また、高度な分析のためにメトリック データを Log Analytics にストリーミングする場合は、Log Analytics によっても課金されます。

次のメトリックによって、サービスの正常性の概要が提供されます。 

> [!NOTE]
> いくつかのメトリックは別の名前で移行されているため、非推奨にしています。 これにより、参照の更新が必要になる可能性があります。 "非推奨" というキーワードでマークされているメトリックは、将来サポートされなくなります。

すべてのメトリック値が 1 分ごとに Azure Monitor に送信されます。 時間の粒度は、メトリック値が提供される時間間隔を定義します。 すべての Service Bus メトリックに対してサポートされる時間間隔は 1 分です。

## <a name="request-metrics"></a>要求のメトリック

データおよび管理操作要求の数をカウントします。

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
| 受信要求| 指定された期間にわたって Service Bus サービスに対して実行された要求の数。 <br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|成功した要求|指定された期間にわたって Service Bus サービスに対して実行された成功した要求の数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|サーバー エラー|指定された期間にわたって Service Bus サービスでエラーのために処理されなかった要求の数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|ユーザー エラー (次のサブセクションを参照)|指定された期間にわたってユーザー エラーのために処理されなかった要求の数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|調整された要求数|使用量を超えたため調整された要求の数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|

### <a name="user-errors"></a>ユーザー エラー

次の 2 種類のエラーは、ユーザー エラーとして分類されます。

1. クライアント側のエラー (HTTP では 400 エラー)。
2. [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) など、メッセージの処理中に発生するエラー。


## <a name="message-metrics"></a>メッセージのメトリック

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
|受信メッセージ|指定された期間にわたって Service Bus に送信されたイベントまたはメッセージの数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
|送信メッセージ|指定された期間にわたって Service Bus から受信されたイベントまたはメッセージの数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|
| メッセージ| キュー/トピック内のメッセージの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> ディメンション:EntityName |
| ActiveMessages| キュー/トピック内のアクティブなメッセージの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/> ディメンション:EntityName |
| 配信不能メッセージ| キュー/トピック内の配信不能メッセージの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均 <br/>ディメンション:EntityName |
| スケジュール設定されたメッセージ| キュー/トピック内のスケジュール済みメッセージの数。 <br/><br/> 単位:Count <br/> 集計の種類:平均  <br/> ディメンション:EntityName |

## <a name="connection-metrics"></a>接続のメトリック

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
|ActiveConnections|名前空間およびエンティティ上のアクティブな接続の数。<br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> ディメンション:EntityName|

## <a name="resource-usage-metrics"></a>リソース使用状況のメトリック

> [!NOTE] 
> 次のメトリックは**プレミアム**層でのみ使用可能です。 

| メトリックの名前 | 説明 |
| ------------------- | ----------------- |
|名前空間あたりの CPU 使用率|名前空間の CPU 使用率 (%)。<br/><br/> 単位:Percent <br/> 集計の種類:最大値 <br/> ディメンション:EntityName|
|名前空間あたりのメモリ サイズの使用量|名前空間のメモリ使用率 (%)。<br/><br/> 単位:Percent <br/> 集計の種類:最大値 <br/> ディメンション:EntityName|

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure Service Bus は、Azure Monitor でのメトリックの次のディメンションをサポートします。 メトリックへのディメンションの追加は省略可能です。 ディメンションを追加しない場合、メトリックは名前空間レベルで指定されます。 

|ディメンション名|説明|
| ------------------- | ----------------- |
|EntityName| Service Bus は、名前空間の下のメッセージング エンティティをサポートします。|

## <a name="set-up-alerts-on-metrics"></a>メトリックに対するアラートの設定

1. **[Service Bus 名前空間]** ページの **[メトリック]** タブで、 **[Configure alerts]** \(アラートの構成\) を選択します。 

    ![[メトリック] ページ - [Configure alerts]\(アラートの構成\) メニュー](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. **[ターゲットの選択]** オプションを選択し、 **[リソースの選択]** ページで次のアクションを実行します。 
    1. **[リソースの種類でフィルター]** フィールドで **[Service Bus 名前空間]** を選択します。 
    2. **[サブスクリプション別でフィルター]** フィールドでサブスクリプションを選択します。
    3. 一覧から **[Service Bus 名前空間]** を選択します。 
    4. **[完了]** を選択します。 
    
        ![名前空間の選択](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. **[条件の追加]** を選択し、 **[シグナル ロジックの構成]** ページで次のアクションを実行します。
    1. **[シグナルの種類]** で **[メトリック]** を選択します。 
    2. シグナルを選択します。 例: **サービス エラー**。 

        ![サーバー エラーの選択](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. **[条件]** で **[より大きい]** を選択します。
    2. **[時間の集計]** で **[合計]** を選択します。 
    3. **[しきい値]** に「**5**」を入力します。 
    4. **[完了]** を選択します。    

        ![条件の指定](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. **[ルールの作成]** ページで、 **[アラートの詳細を定義します]** を展開し、次のアクションを実行します。
    1. アラートの**名前**を入力します。 
    2. アラートの**説明**を入力します。
    3. アラートの**重要度**を選択します。 

        ![[アラートの詳細]](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. **[ルールの作成]** ページで、 **[アクション グループを定義します]** を展開し、 **[新しいアクション グループ]** を選択し、 **[アクション グループの追加]** ページで次のアクションを実行します。 
    1. アクション グループの名前を入力します。
    2. アクション グループの短い名前を入力します。 
    3. サブスクリプションを選択します。 
    4. リソース グループを選択します。 
    5. このチュートリアルでは、 **[アクション名]** に「**Send email**」と入力します。
    6. **[アクションの種類]** で、 **[電子メール/SMS/プッシュ/音声]** を選択します。 
    7. **[詳細の編集]** を選択します。 
    8. **[電子メール/SMS/プッシュ/音声]** ページで、次のアクションを実行します。
        1. **[電子メール]** を選択します。 
        2. **電子メール アドレス**を入力します。 
        3. **[OK]** を選択します。

            ![[アラートの詳細]](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. **[アクション グループの追加]** ページで、 **[OK]** を選択します。 
1. **[ルールの作成]** ページで、 **[アラート ルールの作成]** を選択します。 

    ![[アラート ルールの作成] ボタン](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>次の手順

「[Azure Monitor の概要](../monitoring-and-diagnostics/monitoring-overview.md)」を参照してください。

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


