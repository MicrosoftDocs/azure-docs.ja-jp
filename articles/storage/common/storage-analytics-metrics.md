---
title: Azure Storage Analytics のメトリック (クラシック)
description: Azure Storage で Storage Analytics メトリックを使用する方法について説明します。 トランザクション メトリックと容量メトリック、メトリックの格納方法、メトリックの有効化などについて説明します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: d900ffa4481ba2b6deb21a8325f3f8def8084f84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714733"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics のメトリック (クラシック)

**2023 年 8 月 31 日** をもって、Storage Analytics メトリック ("*クラシック メトリック*" とも呼ばれます) は廃止されます。 詳細については、[公式告知](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)を参照してください。 クラシック メトリックを使用している場合は、その日より前に Azure Monitor のメトリックに移行するようにしてください。 この記事は、移行を行う際に役立ちます。 

Azure Storage では、Storage Analytics を利用し、ストレージ サービスへの要求に関して集計されたトランザクション統計情報と容量データを含むメトリックが格納されます。 トランザクションは API 操作レベルとストレージ サービス レベルで報告されます。 容量はストレージ サービス レベルで報告されます。 メトリック データを使用して次のことを行うことができます。
- ストレージ サービスの使用状況の分析。
- ストレージ サービスに対して行われた要求の問題の診断。
- サービスを使用するアプリケーションのパフォーマンスの向上。

 新しいストレージ アカウントでは、Storage Analytics Metrics が既定で有効になっています。 メトリックは、PowerShell を使用するか Azure CLI を使用して [Azure portal](https://portal.azure.com/) で構成することができます。 ステップ バイ ステップ ガイダンスについては、[Azure Storage Analytics メトリックを有効にして管理する (クラシック)](./manage-storage-analytics-logs.md) に関する記事を参照してください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 サービスごとに Storage Analytics を有効にするには、サービス プロパティの設定操作を行います。  

> [!NOTE]
> Storage Analytics メトリックは、Azure Blob ストレージ、Azure Queue ストレージ、Azure Table ストレージ、Azure Files でお使いいただけます。
> Storage Analytics メトリックはクラシック メトリックになりました。 Microsoft では、Storage Analytics メトリックの代わりに、[Azure Monitor のストレージ メトリック](../blobs/monitor-blob-storage.md)の使用をお勧めしています。

## <a name="transaction-metrics"></a>トランザクション メトリック  
 各ストレージ サービスと要求された API 操作について、受信と送信、可用性、エラー、分類された要求のパーセンテージを含む信頼性の高いデータのセットが 1 時間または 1 分間隔で記録されます。 トランザクションの詳細の一覧については、[Storage Analytics メトリックのテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)に関するページをご覧ください。  

 トランザクション データは、サービス レベルと API 操作レベルで記録されます。 サービス レベルでは、サービスに対して要求が行われなかった場合でも、すべての要求された API 操作を要約した統計情報が 1 時間ごとにテーブル エンティティに書き込まれます。 API 操作レベルでは、統計情報は、その時間内に操作が要求された場合にのみエンティティに書き込まれます。  

 たとえば、Blob service で **GetBlob** 操作を実行する場合、Storage Analytics メトリックでは、その要求がログに記録され、Blob service と **GetBlob** 操作の集計データに要求が含められます。 時間内に **GetBlob** 操作が要求されなかった場合、その操作に対してエンティティが *$MetricsTransactionsBlob* に書き込まれることはありません。  

 トランザクション メトリックは、ユーザー要求と Storage Analytics 自体によって発行された要求に対して記録されます。 たとえば、Storage Analytics からのログとテーブル エンティティの書き込み要求は記録されます。

## <a name="capacity-metrics"></a>容量メトリック  

> [!NOTE]
>  現在、容量メトリックは、Blob service に対してのみご利用になれます。

 容量データはストレージ アカウントの Blob service に対して毎日記録され、2 つのテーブル エンティティが書き込まれます。 一方のエンティティは、ユーザー データの統計情報です。もう一方のエンティティは、Storage Analytics によって使用される `$logs` BLOB コンテナーに関する統計情報です。 *$MetricsCapacityBlob* テーブルには、次の統計情報が含まれています。  

- **Capacity**:ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。  
- **ContainerCount**:ストレージ アカウントの Blob service 内のコンテナーの数。  
- **ObjectCount**:ストレージ アカウントの Blob service 内のコミット済みとコミット前のブロック BLOB またはページ BLOB の数。  

  容量メトリックの詳細については、[Storage Analytics メトリックのテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)に関するページをご覧ください。  

## <a name="how-metrics-are-stored"></a>メトリックの保存  

 各ストレージ サービスのすべてのメトリック データは、そのサービスに予約された 3 つのテーブルに格納されます。 1 つはトランザクション情報用、1 つは分単位のトランザクション情報用、1 つは容量情報用のテーブルです。 トランザクション情報と分単位のトランザクション情報は、要求データと応答データから構成されます。 容量情報は、ストレージ使用量データから構成されます。 ストレージ アカウントの Blob service に関する時間単位のメトリック、分単位のメトリック、容量は、次の表に示す名前のテーブルで利用できます。  

|メトリック レベル|テーブル名|サポートされているバージョン|  
|-------------------|-----------------|----------------------------|  
|時間単位のメトリック、1 次拠点|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|2013 年 8 月 15 日より前のバージョンのみ。 これらの名前はまだサポートされていますが、以下のテーブルを使用するように切り替えることをお勧めします。|  
|時間単位のメトリック、1 次拠点|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|すべてのバージョン。 ファイル サービス メトリックのサポートは、バージョン 2015 年 4 月 5 日以降でのみ使用可能です。|  
|分単位のメトリック、1 次拠点|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|すべてのバージョン。 ファイル サービス メトリックのサポートは、バージョン 2015 年 4 月 5 日以降でのみ使用可能です。|  
|時間単位のメトリック、2 次拠点|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|すべてのバージョン。 読み取りアクセスの地理冗長レプリケーションを有効にする必要があります。|  
|分単位のメトリック、2 次拠点|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|すべてのバージョン。 読み取りアクセスの地理冗長レプリケーションを有効にする必要があります。|  
|容量 (Blob service のみ)|$MetricsCapacityBlob|すべてのバージョン。|  

 これらのテーブルは、Storage Analytics がストレージ サービス エンドポイントに対して有効化されたときに自動的に作成されます。 これらのテーブルには、ストレージ アカウントの名前空間を介してアクセスします (例: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`)。 メトリック テーブルは、操作の一覧には表示されません。また、テーブル名を使用して直接アクセスする必要があります。

## <a name="metrics-alerts"></a>メトリック アラート
ストレージ サービスの動作の重要な変更が自動的に通知されるように、[Azure Portal](https://portal.azure.com) でアラートを設定することをご検討ください。 ステップバイステップ ガイダンスについては、[メトリック アラートの作成](./manage-storage-analytics-logs.md)に関する記事を参照してください。

Storage Explorer ツールを使ってこのメトリック データを区切り形式でダウンロードすると、Microsoft Excel を使ってデータを分析できます。 利用できる Storage Explorer ツールの一覧については、[Azure Storage クライアント ツール](./storage-explorers.md)に関するページをご覧ください。

> [!IMPORTANT]
> ストレージ イベントが発生してから、対応する時間または分単位のメトリック データが記録されるまでに、遅延が生じる場合があります。 分単位のメトリックの場合は、数分間のデータが一度に書き込まれることがあります。 この問題によって、前の数分間のトランザクションが現在の分のトランザクションに集計される可能性があります。 この問題が発生したとき、アラート サービスが構成されているアラート間隔のすべての利用可能なメトリック データを取得できず、アラートが予期せず発生する場合があります。
>

## <a name="billing-on-storage-metrics"></a>ストレージ メトリックの課金
メトリックのテーブル エンティティを作成する要求を記述すると、すべての Azure Storage 操作に適用される標準料金で課金されます。  

クライアントによってメトリック データの読み取りまたは削除が要求された場合も、標準料金で課金されます。 データ保持ポリシーを構成した場合、Azure Storage が古いメトリック データを削除するときには課金されません。 分析データを削除する場合は、削除操作に対してアカウントに請求が届きます。  

メトリック テーブルで使用される容量も課金対象です。 次の情報を利用し、メトリック データの保存に使用される容量を見積もることができます。  

-   あるサービスがすべてのサービスのすべての API を毎時間利用する場合、サービス レベルと API レベルの概要を有効にしていれば、約 148 KB のデータがメトリック トランザクション テーブルに毎時間保存されます。  
-   あるサービスがそのサービスのすべての API を毎時間利用する場合、サービス レベルの概要だけを有効にしているときは、約 12 KB のデータがメトリック トランザクション テーブルに毎時間保存されます。  
-   ログを選択している場合は、BLOB の容量テーブルに、毎日 2 行が追加されます。 このシナリオでは、このテーブルのサイズは、毎日、最大約 300 バイト増えることになります。

## <a name="next-steps"></a>次のステップ
* [ストレージ アカウントを監視する](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Storage Analytics メトリックのテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Storage Analytics によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Storage Analytics のログ記録](storage-analytics-logging.md)
