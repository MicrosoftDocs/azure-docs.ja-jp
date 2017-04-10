---
title: "Azure Storage Analytics を使用したログとメトリック データの収集 | Microsoft Docs"
description: "Storage Analytics では、すべてのストレージ サービスのメトリック データを追跡し、BLOB、キュー、Table Storage のログを収集できます。"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 4680985e5c66444ca8a356cc92d45dc0f1838f55
ms.lasthandoff: 03/06/2017


---
# <a name="storage-analytics"></a>Storage Analytics

Azure Storage Analytics では、ログが記録され、ストレージ アカウントのメトリック データを得ることができます。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断を行うことができます。

Storage Analytics を使用するには、監視するサービスごとに Storage Analytics を個別に有効にする必要があります。 これは、 [Azure ポータル](https://portal.azure.com)から有効にできます。 詳細については、「 [Azure ポータルでのストレージ アカウントの監視](storage-monitor-storage-account.md)」をご覧ください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 各サービスで Storage Analytics を有効にするには、[Get Blob Service Properties](https://msdn.microsoft.com/library/hh452239.aspx)、[Get Queue Service Properties](https://msdn.microsoft.com/library/hh452243.aspx)、[Get Table Service Properties](https://msdn.microsoft.com/library/hh452238.aspx)、[Get File Service Properties](https://msdn.microsoft.com/library/mt427369.aspx) の各操作を使用します。

集計データは、既知の BLOB (ログの場合) と既知のテーブル (メトリックの場合) に格納されます。集計データには、BLOB サービスとテーブル サービスの API を使用してアクセスできます。

ストレージ アカウントの合計の制限とは別に、Storage Analytics には、格納されたデータの量に関して 20 TB の制限があります。 課金ポリシーとデータ保持ポリシーの詳細については、「 [Storage Analytics と課金](https://msdn.microsoft.com/library/hh360997.aspx)」をご覧ください。 ストレージ アカウントの制限の詳細については、「 [Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)」を参照してください。

Storage Analytics や他のツールを使用した Azure Storage 関連の問題の特定、診断、トラブルシューティングに関する詳しいガイドについては、「 [Microsoft Azure ストレージの監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)」をご覧ください。

## <a name="about-storage-analytics-logging"></a>Storage Analytics Logging について
Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。 この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。

ログ エントリが作成されるのは、ストレージ サービス アクティビティが存在する場合に限られます。 たとえば、ストレージ アカウントの BLOB サービスにはアクティビティが存在するが、Table サービスや Queue サービスにはアクティビティが存在しない場合、BLOB サービスに関連したログだけが作成されます。

Storage Analytics Logging は、Azure File Service では使用できません。

### <a name="logging-authenticated-requests"></a>認証済み要求のログ記録
次のタイプの認証済み要求が記録されます。

* 成功した要求
* 失敗した要求 (タイムアウト、帯域幅調整、ネットワーク、承認などに関する各種エラー)
* Shared Access Signature (SAS) を使用した要求 (失敗した要求と成功した要求を含む)
* データの分析要求

Storage Analytics そのものによる要求 (ログの作成/削除など) は記録されません。 ログに記録されるデータの一覧については、「[Storage Analytics によって記録される操作およびステータス メッセージ](https://msdn.microsoft.com/library/hh343260.aspx)」および「[Storage Analytics のログの形式](https://msdn.microsoft.com/library/hh343259.aspx)」をご覧ください。

### <a name="logging-anonymous-requests"></a>匿名要求のログ記録
次のタイプの匿名要求が記録されます。

* 成功した要求
* サーバー エラー
* クライアントとサーバーの両方のタイムアウト エラー
* エラー コード 304 (Not Modified) で失敗した GET 要求

その他の失敗した匿名要求は一切記録されません。 ログに記録されるデータの一覧については、「[Storage Analytics によって記録される操作およびステータス メッセージ](https://msdn.microsoft.com/library/hh343260.aspx)」および「[Storage Analytics のログの形式](https://msdn.microsoft.com/library/hh343259.aspx)」をご覧ください。

### <a name="how-logs-are-stored"></a>ログの保存方法
すべてのログは、Storage Analytics をストレージ アカウントに対して有効にしたときに自動的に作成される $logs という名前のコンテナー内のブロック BLOB に格納されます。 $logs コンテナーは、ストレージ アカウントの BLOB 名前空間にあります (例: `http://<accountname>.blob.core.windows.net/$logs`)。 Storage Analytics を有効にした後は、このコンテナーを削除することはできません。ただし、コンテナーの内容を削除することはできます。

> [!NOTE]
> コンテナーの一覧作成操作 ( [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) メソッドなど) を実行しても、$logs コンテナーは表示されません。 直接アクセスする必要があります。 たとえば、[ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) メソッドを使用して、`$logs` コンテナー内の BLOB にアクセスできます。
> 要求がログに記録されると、Storage Analytics は、中間結果をブロックとしてアップロードします。 これらのブロックを定期的にコミットし、BLOB として利用できるようにします。
> 
> 

同じ時間内に作成されたログについて、重複するレコードが存在する場合があります。 レコードが重複しているかどうかは、**RequestId** と **Operation** の数をチェックすることによって確認できます。

### <a name="log-naming-conventions"></a>ログの名前付け規則
各ログは次の形式で出力されます。

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

次の表は、ログ名を構成する各属性の説明です。

| Attribute | Description |
| --- | --- |
| <service-name> |ストレージ サービスの名前。 例: blob、table、または queue |
| YYYY |ログの&4; 桁表記の年。 例: 2011 |
| MM |ログの&2; 桁表記の月。 例: 07 |
| DD |ログの&2; 桁表記の月。 例: 07 |
| hh |ログの開始時刻 (時) を示す 2 桁の数字 (24 時間制 UTC 形式)。 例: 18 |
| MM |ログの開始時刻 (分) を示す&2; 桁の数字。 現在のバージョンの Storage Analytics ではこの値はサポートされず、常に 00 になります。 |
| <counter> |1 時間おきにストレージ サービスに対して生成されるログ BLOB の数を示す&0; から始まる&6; 桁のカウンター。 このカウンターの初期値は 000000 です。 例: 000001 |

以上の例をすべて組み合わせたサンプルのログ名は、次のようになります。

    blob/2011/07/31/1800/000001.log

前のログにアクセスするための URI の例を次に示します。

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

ストレージ要求がログに記録されるとき、生成されるログの名前は、要求された操作が完了した時刻が基準となります。 たとえば、GetBlob 要求が 2011 年 7 月 31 日午後 6 時 30 分に完了した場合、 ログには次のプレフィックス (`blob/2011/07/31/1800/`) が書き込まれます。

### <a name="log-metadata"></a>ログのメタデータ
すべてのログ BLOB はメタデータと共に格納されます。このメタデータを使って、BLOB に含まれるログ データを特定できます。 それぞれのメタデータ属性について次の表で説明します。

| Attribute | Description |
| --- | --- |
| LogType |読み取り、書き込み、削除の各操作に関連した情報がログに含まれているかどうかを表します。 この値には、操作の種類が&1; つだけ含まれている場合もあれば、3 つすべてがコンマ区切りで記録されている場合もあります。 例 1: write 例 2: read,write 例 3: read,write,delete |
| StartTime |ログに含まれる最も古いエントリの時刻です (YYYY-MM-DDThh:mm:ssZ 形式)。 例: 2011-07-31T18:21:46Z |
| EndTime |ログに含まれる最も新しいエントリの時刻です (YYYY-MM-DDThh:mm:ssZ 形式)。 例: 2011-07-31T18:22:09Z |
| LogVersion |ログのフォーマットのバージョン。 現在サポートされている値: 1.0 |

以上の例を使用したサンプル メタデータを次に示します。

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion=1.0

### <a name="accessing-logging-data"></a>ログ データへのアクセス
`$logs` コンテナー内のすべてのデータには、BLOB サービス API (Azure マネージ ライブラリで提供される .NET API など) を使用してアクセスできます。 ストレージ アカウント管理者は、ログの読み取りと削除を行うことができますが、ログの作成または更新を行うことはできません。 ログの照会には、ログのメタデータとログの名前の両方を使用できます。 特定の時間のログが順番に並んでいない場合もありますが、メタデータには、ログに含まれているエントリのタイムスパンが必ず記録されています。 したがって、ログの名前とメタデータを組み合わせれば、特定のログを検索できます。

## <a name="about-storage-analytics-metrics"></a>Storage Analytics Metrics について
Storage Analytics では、ストレージ サービスに対する要求に関する集計されたトランザクション統計情報と容量データを含むメトリックを格納できます。 トランザクションに関しては、API 操作レベルとストレージ サービス レベルの両方でレポートされます。容量に関しては、ストレージ サービス レベルでレポートされます。 メトリック データは、ストレージ サービスの使用状況の分析、ストレージ サービスに対する要求に関する問題の診断、サービスを使用するアプリケーションのパフォーマンスの向上に利用できます。

Storage Analytics を使用するには、監視するサービスごとに Storage Analytics を個別に有効にする必要があります。 これは、 [Azure ポータル](https://portal.azure.com)から有効にできます。 詳細については、「 [Azure ポータルでのストレージ アカウントの監視](storage-monitor-storage-account.md)」をご覧ください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 サービスごとに Storage Analytics を有効にするには、 **Get Service Properties** 操作を使用します。

### <a name="transaction-metrics"></a>トランザクション メトリック
各ストレージ サービスと要求された API 操作について、受信/送信、可用性、エラー、分類された要求のパーセンテージを含む信頼性の高いデータのセットが&1; 時間または&1; 分間隔で記録されます。 トランザクションの詳細の一覧については、「 [Storage Analytics Metrics のテーブル スキーマ](https://msdn.microsoft.com/library/hh343264.aspx) 」をご覧ください。

トランザクション データは、サービス レベルと API 操作レベルの&2; つのレベルで記録されます。 サービス レベルでは、サービスに対して要求が行われなかった場合でも、すべての要求された API 操作を要約した統計情報が&1; 時間ごとにテーブル エンティティに書き込まれます。 API 操作レベルでは、統計情報は、その時間内に操作が要求された場合にのみエンティティに書き込まれます。

たとえば、BLOB サービスで **GetBlob** 操作を実行する場合、Storage Analytics Metrics はその要求を記録し、BLOB サービスと **GetBlob** 操作の両方の集計データに要求を含めます。 ただし、時間内に **GetBlob** 操作が要求されなかった場合、その操作に対してエンティティが `$MetricsTransactionsBlob` に書き込まれることはありません。

トランザクション メトリックは、ユーザー要求と Storage Analytics 自体によって発行された要求の両方に関して記録されます。 たとえば、Storage Analytics からのログとテーブル エンティティの書き込み要求は記録されます。 これらの要求の課金方法の詳細については、「 [Storage Analytics と課金](https://msdn.microsoft.com/library/hh360997.aspx)」をご覧ください。

### <a name="capacity-metrics"></a>容量メトリック
> [!NOTE]
> 現在、容量メトリックは、BLOB サービスに対してのみ利用できます。 Table サービスと Queue サービスに関する容量メトリックは、Storage Analytics の将来のバージョンでの提供を予定しています。
> 
> 

容量データはストレージ アカウントの BLOB サービスに対して毎日記録され、2 つのテーブル エンティティが書き込まれます。 一方のエンティティは、ユーザー データの統計情報です。もう一方のエンティティは、Storage Analytics によって使用される `$logs` BLOB コンテナーに関する統計情報です。 `$MetricsCapacityBlob` テーブルには、次の統計情報が含まれています。

* **Capacity**: ストレージ アカウントの BLOB サービスによって使用されているストレージの量 (バイト単位)。
* **ContainerCount**: ストレージ アカウントの BLOB サービス内の BLOB コンテナーの数。
* **ObjectCount**: ストレージ アカウントの BLOB サービス内のコミット済みとコミット前のブロック BLOB またはページ BLOB の数。

容量メトリックの詳細については、「 [Storage Analytics Metrics のテーブル スキーマ](https://msdn.microsoft.com/library/hh343264.aspx)」をご覧ください。

### <a name="how-metrics-are-stored"></a>メトリックの保存
各ストレージ サービスのすべてのメトリック データはそのサービスに予約された次の&3; つのテーブルに格納されます。トランザクション情報用に&1; つ、分単位のトランザクション情報用に&1; つ、容量情報用に&1; つのテーブルです。 トランザクション情報と分単位のトランザクション情報は、要求データと応答データから構成されます。容量情報は、ストレージ使用量データから構成されます。 ストレージ アカウントの BLOB サービスに関する時間単位のメトリック、分単位のメトリック、容量は、次の表に示す名前のテーブルで利用できます。

| メトリック レベル | テーブル名 | サポートされているバージョン |
| --- | --- | --- |
| 時間単位のメトリック、1 次拠点 |$MetricsTransactionsBlob  <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |2013-08-15 よりも前のバージョンのみ。 これらの名前はまだサポートされていますが、下に示すテーブルを使用するように切り替えることをお勧めします。 |
| 時間単位のメトリック、1 次拠点 |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |2013-08-15 を含むすべてのバージョン。 |
| 分単位のメトリック、1 次拠点 |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |2013-08-15 を含むすべてのバージョン。 |
| 時間単位のメトリック、2 次拠点 |$MetricsHourSecondaryTransactionsBlob  <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |2013-08-15 を含むすべてのバージョン。 読み取りアクセスの地理冗長レプリケーションを有効にする必要があります。 |
| 分単位のメトリック、2 次拠点 |$MetricsMinuteSecondaryTransactionsBlob  <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |2013-08-15 を含むすべてのバージョン。 読み取りアクセスの地理冗長レプリケーションを有効にする必要があります。 |
| 容量 (BLOB サービスのみ) |$MetricsCapacityBlob |2013-08-15 を含むすべてのバージョン。 |

これらのテーブルは、Storage Analytics がストレージ アカウントに対して有効化されたときに自動的に作成されます。 これらのテーブルには、ストレージ アカウントの名前空間を介してアクセスします (例: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>メトリック データへのアクセス
メトリック テーブル内のすべてのデータは、テーブル サービス API を使用してアクセスできます (Azure マネージ ライブラリで提供される .NET API など)。 ストレージ アカウント管理者は、テーブル エンティティの読み取りと削除を行うことができますが、テーブル エンティティの作成または更新を行うことはできません。

## <a name="billing-for-storage-analytics"></a>Storage Analytics の課金
すべてのメトリック データは、ストレージ アカウントのサービスによって書き込まれます。 したがって、Storage Analytics によって実行される個々の書き込み操作には料金が発生します。 加えて、メトリック データに費やされるストレージの使用量も課金対象となります。

Storage Analytics によって実行される次の操作には料金が発生します。

* ログの BLOB の作成要求 
* メトリックのテーブル エンティティの作成要求

データ保持ポリシーを構成した場合、Storage Analytics が古いログ データやメトリック データを削除しますが、その際の削除トランザクションに対する料金は発生しません。 ただし、クライアントからの削除トランザクションは課金対象となります。 保持ポリシーの詳細については、「 [Storage Analytics のデータ保持ポリシーの設定](https://msdn.microsoft.com/library/azure/hh343263.aspx)」をご覧ください。

### <a name="understanding-billable-requests"></a>課金の対象となる要求について
アカウントのストレージ サービスに対して実行されるそれぞれの要求は、課金対象の要求とそうでない要求とに分けられます。 Storage Analytics は、サービスに対するすべての要求をそれぞれログに記録します。たとえば、要求がどのように処理されたかを示すステータス メッセージも記録されます。 同様に、Storage Analytics は、サービスに対するメトリックと、そのサービスの API 操作に対するメトリックを保存しています (特定のステータス メッセージの数やパーセンテージなど)。 これらの機能をうまく組み合わせて利用すると、課金対象の要求を分析したり、アプリケーションを改善したり、サービスに対する要求の問題を診断したりできます。 課金の詳細については、「[Understanding Azure Storage Billing - Bandwidth, Transactions, and Capacity (Azure Storage の課金について - 帯域幅、トランザクション、容量)](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)」をご覧ください。

Storage Analytics のデータで課金対象の要求を調べるときには、「 [Storage Analytics によって記録される操作やステータス メッセージ](https://msdn.microsoft.com/library/azure/hh343260.aspx) 」の表が参考になります。 手元のログ データとメトリック データをステータス メッセージと照らし合わせながら、特定の要求が課金対象であったかどうかを確認できます。 この表は、ストレージ サービスまたは特定の API 操作の可用性を調べるときにも利用できます。

## <a name="next-steps"></a>次のステップ
### <a name="setting-up-storage-analytics"></a>Storage Analytics の設定
* [Azure ポータルでのストレージ アカウントの監視](storage-monitor-storage-account.md)
* [Storage Analytics の有効化と構成](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Storage Analytics Logging
* [Storage Analytics Logging について](https://msdn.microsoft.com/library/hh343262.aspx)
* [Storage Analytics のログの形式](https://msdn.microsoft.com/library/hh343259.aspx)
* [Storage Analytics によって記録される操作やステータス メッセージ](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Storage Analytics Metrics
* [Storage Analytics Metrics について](https://msdn.microsoft.com/library/hh343258.aspx)
* [Storage Analytics Metrics のテーブル スキーマ](https://msdn.microsoft.com/library/hh343264.aspx)
* [Storage Analytics によって記録される操作やステータス メッセージ](https://msdn.microsoft.com/library/hh343260.aspx)  


