---
title: Azure Storage Analytics のログ
description: Storage Analytics を使用して、Azure Storage 要求の詳細をログします。 ログの対象となる要求、ログの格納方法、Storage のログを有効にする方法などについて説明します。
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: a5d1d6af68fcbd6a5822b2652ee79c464d02241f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200762"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage Analytics のログ

Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。 この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。 これは、ほとんどの要求ではログ レコードが生成されますが、Storage Analytics ログの完全性と適時性は保証されないことを意味します。 

> [!NOTE]
> Microsoft では、Storage Analytics ログの代わりに、Azure Monitor の Azure Storage ログを使用することをお勧めしています。 Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、およびテーブルに対してログが有効になります。 詳細については、以下のいずれかの記事をお読みください。
>
> - [Azure Blob Storage の監視](../blobs/monitor-blob-storage.md)
> - [Azure Files の監視](../files/storage-files-monitoring.md)
> - [Azure Queue Storage の監視](../queues/monitor-queue-storage.md)
> - [Azure Table ストレージの監視](../tables/monitor-table-storage.md)

 既定では、お使いのストレージ アカウントで Storage Analytics のログは有効になっていません。 これは、[Azure portal](https://portal.azure.com/) で、または PowerShell や Azure CLI を使用して有効にすることができます。 ステップ バイ ステップ ガイダンスについては、「[Azure Storage Analytics ログを有効にして管理する (クラシック)](manage-storage-analytics-logs.md)」を参照してください。 

また、プログラムから REST API またはクライアント ライブラリを使用して Storage Analytics ログを有効にすることもできます。 各サービスで Storage Analytics を有効にするには、[Get Blob Service Properties](/rest/api/storageservices/Blob-Service-REST-API)、[Get Queue Service Properties](/rest/api/storageservices/Get-Queue-Service-Properties)、および [Get Table Service Properties](/rest/api/storageservices/Get-Table-Service-Properties) の各操作を使用します。 .NET を使用して Storage Analytics ログを有効にする例については、[ログの有効化](manage-storage-analytics-logs.md)に関するページを参照してください

 ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントの BLOB エンドポイントにはアクティビティが存在するが、Table エンドポイントや Queue エンドポイントには存在しない場合、Blob service に関連したログだけが作成されます。

> [!NOTE]
>  現在、Storage Analytics のログは、BLOB、Queue、Table の各サービスでのみ使用できます。 Storage Analytics のログは、Premium パフォーマンス [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) アカウントでも使用できます。 ただし、Premium パフォーマンスを持つ汎用 v2 アカウントでは使用できません。

## <a name="requests-logged-in-logging"></a>ログで記録される要求
### <a name="logging-authenticated-requests"></a>認証済み要求のログ記録

 次のタイプの認証済み要求が記録されます。

- 成功した要求
- 失敗した要求 (タイムアウト、スロットル、ネットワーク、承認などに関する各種エラー)
- Shared Access Signature (SAS) または OAuth を使用した要求 (失敗した要求と成功した要求を含む)
- データの分析要求

  Storage Analytics そのものによる要求 (ログの作成/削除など) は記録されません。 ログに記録されるデータの一覧については、「[Storage Analytics によって記録される操作およびステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)」および「[Storage Analytics のログの形式](/rest/api/storageservices/storage-analytics-log-format)」をご覧ください。

### <a name="logging-anonymous-requests"></a>匿名要求のログ記録

 次のタイプの匿名要求が記録されます。

- 成功した要求
- サーバー エラー
- クライアントとサーバーの両方のタイムアウト エラー
- エラー コード 304 (変更されていません) で失敗した GET 要求

  その他の失敗した匿名要求は一切記録されません。 ログに記録されるデータの一覧については、「[Storage Analytics によって記録される操作およびステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)」および「[Storage Analytics のログの形式](/rest/api/storageservices/storage-analytics-log-format)」をご覧ください。

## <a name="how-logs-are-stored"></a>ログの保存方法

すべてのログは、Storage Analytics をストレージ アカウントに対して有効にしたときに自動的に作成される `$logs` という名前のコンテナー内のブロック BLOB に格納されます。 `$logs` コンテナーは、ストレージ アカウントの BLOB 名前空間にあります (例: `http://<accountname>.blob.core.windows.net/$logs`)。 Storage Analytics を有効にした後は、このコンテナーを削除することはできません。ただし、コンテナーの内容を削除することはできます。 ストレージ閲覧ツールを使用して直接コンテナーに移動した場合は、ログ データを含むすべての BLOB が表示されます。

> [!NOTE]
>  コンテナーの一覧作成操作 ( List Containers 操作など) を実行しても、`$logs` コンテナーは表示されません。 直接アクセスする必要があります。 たとえば、List Blobs 操作を使用して、`$logs` コンテナー内の BLOB にアクセスできます。

要求がログに記録されると、Storage Analytics は、中間結果をブロックとしてアップロードします。 これらのブロックを定期的にコミットし、BLOB として利用できるようにします。 **$logs** コンテナー内の BLOB にログ データが表示されるまでに最長 1 時間かかる場合があります。これは、ストレージ サービスがログ ライターをフラッシュする頻度が原因です。 同じ時間内に作成されたログについて、重複するレコードが存在する場合があります。 レコードが重複しているかどうかは、**RequestId** と **Operation** の数をチェックすることによって確認できます。

複数のファイルに及ぶ大量のログ データが毎時間発生する場合、BLOB メタデータを使用して BLOB メタデータ フィールドを検査すると、ログに含まれているデータを確認できます。 これが便利であるのは、データがログ ファイルに書き込まれる間に遅延が発生することもあるためです。BLOB メタデータを見ると、BLOB 名を見たときよりも正確に BLOB の内容がわかります。

通常、ストレージ閲覧ツールでは、BLOB のメタデータを表示できます。この情報は、PowerShell やプログラムを使用して読み取ることもできます。 以下の PowerShell スニペットは、ログ BLOB の一覧をフィルタリングする例を示しています。名前でフィルタリングして時間を指定する部分と、メタデータでフィルタリングして **write** 操作を含むログのみを識別する部分が含まれています。  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

プログラムで BLOB を一覧表示する方法については、「[Enumerating Blob Resources (Blob リソースの列挙)](/rest/api/storageservices/Enumerating-Blob-Resources)」と「[Setting and Retrieving Properties and Metadata for Blob Resources (BLOB リソースのプロパティとメタデータの設定および取得)](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources)」を参照してください。  

### <a name="log-naming-conventions"></a>ログの名前付け規則

 各ログは以下の形式で書き込まれます。

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 以下の表は、ログ名内の各属性を説明しています。

|属性|説明|
|---------------|-----------------|
|`<service-name>`|ストレージ サービスの名前。 たとえば、`blob`、`table`、`queue` などがあります。|
|`YYYY`|ログの 4 桁表記の年。 例: `2011`|
|`MM`|ログの 2 桁表記の月。 例: `07`|
|`DD`|ログの 2 桁表記の日。 例: `31`|
|`hh`|ログの開始時刻 (時) を示す 2 桁の数字 (24 時間制 UTC 形式)。 例: `18`|
|`mm`|ログの開始時刻 (分) を示す 2 桁の数字。 **注:** 現在のバージョンの Storage Analytics ではこの値はサポートされず、常に `00` になります。|
|`<counter>`|1 時間おきにストレージ サービスに対して生成されるログ BLOB の数を示す 0 から始まる 6 桁のカウンター。 このカウンターの初期値は `000000` です。 例: `000001`|

 上記の例をすべて組み合わせたサンプルのログ名は、以下のようになります。

 `blob/2011/07/31/1800/000001.log`

 上記のログにアクセスするための URI の例を以下に示します。

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 ストレージ要求がログに記録されるとき、生成されるログの名前は、要求された操作が完了した時刻が基準となります。 たとえば、GetBlob 要求が 2011 年 7 月 31 日の午後 6 時 30 分に完了した場合、書き込まれるログのプレフィックスは、`blob/2011/07/31/1800/` になります。

### <a name="log-metadata"></a>ログのメタデータ

 すべてのログ BLOB はメタデータと共に格納されます。このメタデータを使って、BLOB に含まれるログ データを特定できます。 それぞれのメタデータ属性について以下の表で説明します。

|属性|説明|
|---------------|-----------------|
|`LogType`|読み取り、書き込み、削除の各操作に関連した情報がログに含まれているかどうかを表します。 この値には、操作の種類が 1 つだけ含まれている場合もあれば、3 つすべてがコンマ区切りで記録されている場合もあります。<br /><br /> 例 1: `write`<br /><br /> 例 2: `read,write`<br /><br /> 例 3: `read,write,delete`|
|`StartTime`|ログに含まれる最も古いエントリの時刻です (`YYYY-MM-DDThh:mm:ssZ` 形式)。 例: `2011-07-31T18:21:46Z`|
|`EndTime`|ログに含まれる最も新しいエントリの時刻です (`YYYY-MM-DDThh:mm:ssZ` 形式)。 例: `2011-07-31T18:22:09Z`|
|`LogVersion`|ログのフォーマットのバージョン。|

 上記の例を使用したサンプル メタデータを以下に示します。

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>次のステップ

* [Azure Storage Analytics ログを有効にして管理する (クラシック)](manage-storage-analytics-logs.md)
* [Storage Analytics のログの形式](/rest/api/storageservices/storage-analytics-log-format)
* [Storage Analytics によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Storage Analytics のメトリック (クラシック)](storage-analytics-metrics.md)
