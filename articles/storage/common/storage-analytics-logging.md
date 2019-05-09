---
title: Azure Storage Analytics のログ
description: Azure Storage に対する要求の詳細をログに記録する方法について説明します。
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 09a5a6d823240b724e6ec88de38df068a58982d9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652061"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage Analytics のログ

Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。 この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。

 既定では、お使いのストレージ アカウントで Storage Analytics のログは有効になっていません。 Storage Analytics は [Azure Portal](https://portal.azure.com/) で有効にできます。詳細については、「[Azure Portal でのストレージ アカウントの監視](/azure/storage/storage-monitor-storage-account)」を参照してください。 また、プログラムから REST API またはクライアント ライブラリを使用して有効にすることもできます。 各サービスで Storage Analytics を有効にするには、[Get Blob Service Properties](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)、[Get Queue Service Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)、および [Get Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) の各操作を使用します。

 ログ エントリが作成されるのは、サービス エンドポイントに対して行われた要求がある場合に限られます。 たとえば、ストレージ アカウントの BLOB エンドポイントにはアクティビティが存在するが、Table エンドポイントや Queue エンドポイントには存在しない場合、Blob service に関連したログだけが作成されます。

> [!NOTE]
>  現在、Storage Analytics のログは、BLOB、Queue、Table の各サービスでのみ使用できます。 ただし、Premium ストレージ アカウントは、サポートされません。

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
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

プログラムで BLOB を一覧表示する方法については、「[Enumerating Blob Resources (Blob リソースの列挙)](http://msdn.microsoft.com/library/azure/hh452233.aspx)」と「[Setting and Retrieving Properties and Metadata for Blob Resources (BLOB リソースのプロパティとメタデータの設定および取得)](http://msdn.microsoft.com/library/azure/dd179404.aspx)」を参照してください。  

### <a name="log-naming-conventions"></a>ログの名前付け規則

 各ログは以下の形式で書き込まれます。

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 以下の表は、ログ名内の各属性を説明しています。

|Attribute|説明|
|---------------|-----------------|
|`<service-name>`|ストレージ サービスの名前。 たとえば、`blob`、`table`、`queue` などがあります。|
|`YYYY`|ログの 4 桁表記の年。 次に例を示します。`2011`|
|`MM`|ログの 2 桁表記の月。 次に例を示します。`07`|
|`DD`|ログの 2 桁表記の日。 次に例を示します。`31`|
|`hh`|ログの開始時刻 (時) を示す 2 桁の数字 (24 時間制 UTC 形式)。 次に例を示します。`18`|
|`mm`|ログの開始時刻 (分) を示す 2 桁の数字。 **注:** 現在のバージョンの Storage Analytics ではこの値はサポートされず、常に `00` になります。|
|`<counter>`|1 時間おきにストレージ サービスに対して生成されるログ BLOB の数を示す 0 から始まる 6 桁のカウンター。 このカウンターの初期値は `000000` です。 次に例を示します。`000001`|

 上記の例をすべて組み合わせたサンプルのログ名は、以下のようになります。

 `blob/2011/07/31/1800/000001.log`

 上記のログにアクセスするための URI の例を以下に示します。

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 ストレージ要求がログに記録されるとき、生成されるログの名前は、要求された操作が完了した時刻が基準となります。 たとえば、GetBlob 要求が 2011 年 7 月 31 日の午後 6 時 30 分に完了した場合、書き込まれるログのプレフィックスは、`blob/2011/07/31/1800/` になります。

### <a name="log-metadata"></a>ログのメタデータ

 すべてのログ BLOB はメタデータと共に格納されます。このメタデータを使って、BLOB に含まれるログ データを特定できます。 それぞれのメタデータ属性について以下の表で説明します。

|Attribute|説明|
|---------------|-----------------|
|`LogType`|読み取り、書き込み、削除の各操作に関連した情報がログに含まれているかどうかを表します。 この値には、操作の種類が 1 つだけ含まれている場合もあれば、3 つすべてがコンマ区切りで記録されている場合もあります。<br /><br /> 例 1: `write`<br /><br /> 例 2: `read,write`<br /><br /> 例 3: `read,write,delete`|
|`StartTime`|ログに含まれる最も古いエントリの時刻です (`YYYY-MM-DDThh:mm:ssZ` 形式)。 次に例を示します。`2011-07-31T18:21:46Z`|
|`EndTime`|ログに含まれる最も新しいエントリの時刻です (`YYYY-MM-DDThh:mm:ssZ` 形式)。 次に例を示します。`2011-07-31T18:22:09Z`|
|`LogVersion`|ログのフォーマットのバージョン。|

 上記の例を使用したサンプル メタデータを以下に示します。

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>ストレージ ログの有効化

ストレージ ログは、Azure portal、PowerShell、および Storage SDK で有効にすることができます。

### <a name="enable-storage-logging-using-the-azure-portal"></a>Azure portal を使用したストレージ ログの有効化  

Azure portal では、**[Diagnostics settings (classic)]\(診断の設定 (クラシック)\)** ブレードを使用して、ストレージ ログを制御します。このブレードは、ストレージ アカウントの **[Menu blade]\(メニュー ブレード\)** の **[監視 (クラシック)]** セクションからアクセスできます。

指定できるのは、ログに記録するストレージ サービスと、ログ データの保持期間 (日数) です。  

### <a name="enable-storage-logging-using-powershell"></a>PowerShell を使用したストレージ ログの有効化  

 ローカル マシン上で PowerShell を使用して、ストレージ アカウント内のストレージ ログを構成できます。現在の設定を取得するには、Azure PowerShell コマンドレット **Get-AzureStorageServiceLoggingProperty** を使用し、現在の設定を変更するには、コマンドレット **Set-AzureStorageServiceLoggingProperty** を使用します。  

 ストレージ ログを制御するコマンドレットでは、**LoggingOperations** パラメーターが使用されます。このパラメーターは文字列で、記録する要求の種類がコンマ区切り形式で含まれています。 指定できる要求の種類には、**read**、**write**、および **delete** の 3 つがあります。 ログをオフにするには、**LoggingOperations** パラメーターに **none** の値を指定します。  

 以下のコマンドでは、既定のストレージ アカウント内の Queue サービスで read、write、および delete の各要求に対するログがオンにされます。リテンション期間は 5 日間に設定されています。  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 以下のコマンドでは、既定のストレージ アカウント内の Table サービスに対するログがオフにされます。  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Azure サブスクリプションを処理するように Azure PowerShell コマンドレットを構成する方法と、使用する既定のストレージ アカウントを選択する方法については、[Azure PowerShell のインストールと構成の方法](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)に関する記事をご覧ください。  

### <a name="enable-storage-logging-programmatically"></a>プログラムを使用したストレージ ログの有効化  

 Azure portal または Azure PowerShell コマンドレットを使用してストレージ ログを制御する方法に加え、いずれかの Azure Storage API を使用することもできます。 たとえば、.NET 言語を使用している場合は、ストレージ クライアント ライブラリを使用できます。  

 クラス **CloudBlobClient**、**CloudQueueClient**、および **CloudTableClient** はすべて、**SetServiceProperties** や **SetServicePropertiesAsync** のような、**ServiceProperties** オブジェクトをパラメーターとして使用するメソッドが用意されています。 **ServiceProperties** オブジェクトを使用して、ストレージ ログを構成することができます。 たとえば、以下の C# スニペットは、ログの記録対象、およびキュー ログの保持期間を変更する方法を示しています。  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 .NET 言語を使用してストレージ ログを構成する方法の詳細については、「[Storage Client Library Reference (ストレージ クライアント ライブラリ リファレンス)](https://msdn.microsoft.com/library/azure/dn261237.aspx)」を参照してください。  

 REST API を使用してストレージ ログを構成する方法の概要については、「[Enabling and Configuring Storage Analytics (Storage Analytics の有効化および構成)](https://msdn.microsoft.com/library/azure/hh360996.aspx)」を参照してください。  

## <a name="download-storage-logging-log-data"></a>ストレージ ログのログ データのダウンロード

 ログ データを表示して分析するには、対象のログ データを含む BLOB をローカル マシンにダウンロードする必要があります。 多くのストレージ閲覧ツールでは、ストレージ アカウントから BLOB をダウンロードできます。また、Azure Storage チームが提供するコマンドライン用の Azure Copy Tool (**AzCopy**) を使用して、ログ データをダウンロードすることもできます。  

 対象のログ データをダウンロードし、同じログ データを複数回ダウンロードしないためには、以下のようにします。  

-   同じデータを複数回ダウンロードしないようにするには、ログ データを含む BLOB に対して日付と時刻の名前付け規則を使用し、既にダウンロードしている分析対象の BLOB を追跡します。  

-   ダウンロードする必要がある BLOB を正確に識別するには、ログ データを含む BLOB のメタデータを使用して、BLOB がログ データを保持する期間を確認します。  

> [!NOTE]
>  AzCopy は、Azure SDK に含まれているものですが、[https://aka.ms/AzCopy](https://aka.ms/AzCopy) から最新バージョンをいつでもダウンロードできます。 既定では、AzCopy は、フォルダー **C:\Program Files (x86)\Microsoft SDKs\Windows Azure\AzCopy** 内にインストールされます。コマンド プロンプトまたは PowerShell ウィンドウ内でこのツールを実行しようとするときは、事前にこのフォルダーをパスに追加する必要があります。  

 次の例は、2014 年 5 月 20 日の午前 09 時、午前 10 時、および午前 11 時から Queue サービスのログ データをダウンロードする方法を示しています。 AzCopy で **/S** パラメーターを指定すると、ログ ファイル名内の日時に基づいて、ローカル フォルダー構造が作成されます。**/V** パラメーターを指定すると、詳細な出力が生成されます。**/Y** パラメーターを指定すると、すべてのローカル ファイルが上書きされます。 **<yourstorageaccount\>** は、ストレージ アカウント名に置き換えます。**<yourstoragekey\>** は、ストレージ アカウント キーに置き換えます。  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy には、便利なパラメーターもいくつか用意されています。これらのパラメーターを使用すると、ダウンロードしたファイルの最終変更日時の設定方法を制御したり、ローカル マシン上に既に存在しているファイルより古いファイルをダウンロードするのか、それとも新しいファイルをダウンロードするのかを制御したりできます。 AzCopy は、再起動可能モードで実行することもできます。 詳細については、**AzCopy/?** コマンドを実行して、ヘルプを表示してください。  

 ログ データをプログラムでダウンロードする方法の例については、ブログ投稿「[Windows Azure Storage Logging:Using Logs to Track Storage Requests (Windows Azure Storage ログ: ログを使用してストレージ要求を追跡する)](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx)」を参照し、このページ上で "DumpLogs (ダンプログ)" という語を検索してください。  

 ログ データのダウンロードが完了すると、ファイル内のログ エントリを表示できます。 これらのログ ファイルは、区切り記号付きテキスト形式が使用されているため、Microsoft Message Analyzer などの多くのログ読み取りツールで解析できます (詳細については、「[Microsoft Azure Storage の監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)」を参照してください)。 ログ ファイルの内容を書式設定、フィルタリング、並べ替え、AD 検索するために、各種のツールがさまざまな機能を提供しています。 ストレージ ログのログ ファイルの形式および内容の詳細については、「[Storage Analytics Log Format (Storage Analytics のログ形式)](/rest/api/storageservices/storage-analytics-log-format)」および「[Storage Analytics Logged Operations and Status Message (Storage Analytics によって記録される操作および状態メッセージ)](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Storage Analytics のログの形式](/rest/api/storageservices/storage-analytics-log-format)
* [Storage Analytics によって記録される操作やステータス メッセージ](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Storage Analytics のメトリック (クラシック)](storage-analytics-metrics.md)