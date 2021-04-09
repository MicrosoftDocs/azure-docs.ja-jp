---
title: Azure Storage Analytics ログを有効にして管理する (クラシック) | Microsoft Docs
description: Azure Storage Analytics を使用して、Azure でストレージ アカウントを監視する方法について説明します。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 0c182e1093c29206d27a0e55a46dd9a5607fa6ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701707"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Azure Storage Analytics ログを有効にして管理する (クラシック)

[Azure Storage Analytics](storage-analytics.md) には、BLOB、キュー、テーブル用のログが用意されています。 [Azure portal](https://portal.azure.com) を使用して、アカウントに対してログが記録されるように構成できます。 この記事では、ログを有効にして管理する方法を示します。 メトリックを有効にする方法については、[Azure Storage Analytics メトリックを有効にして管理する (クラシック)]() に関する記事を参照してください。  Azure portal で監視データを調査して格納するとコストがかかります。 詳細については、「[Storage Analytics](storage-analytics.md)」を参照してください。

> [!NOTE]
> Microsoft では、Storage Analytics ログの代わりに、Azure Monitor の Azure Storage ログを使用することをお勧めしています。 Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、およびテーブルに対してログが有効になります。 詳細については、以下のいずれかの記事をお読みください。
>
> - [Azure Blob Storage の監視](../blobs/monitor-blob-storage.md)
> - [Azure Files の監視](../files/storage-files-monitoring.md)
> - [Azure Queue Storage の監視](../queues/monitor-queue-storage.md)
> - [Azure Table ストレージの監視](../tables/monitor-table-storage.md)

Storage Analytics や他のツールを使用した Azure Storage 関連の問題の特定、診断、トラブルシューティングに関する詳しいガイドについては、「[Microsoft Azure Storage の監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)」をご覧ください。

<a id="configure-logging"></a>

## <a name="enable-logs"></a>ログの有効化

BLOB、テーブル、およびキューごとに、読み取り要求、書き込み要求、および削除要求の診断ログを保存するよう Azure Storage に指示できます。 設定したデータの保持ポリシーはこうしたログにも適用されます。

> [!NOTE]
> 現在、Azure Files では、Storage Analytics のメトリックはサポートされていますが、Storage Analytics のログ記録はサポートされていません。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com) で **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択して、ストレージ アカウント ブレードを開きます。

2. メニュー ブレードの **[監視 (クラシック)]** セクションで **[診断設定 (クラシック)]** を選択します。

    ![Azure Portal の [監視] の [診断] メニュー。](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. **[ステータス]** を **[オン]** に設定して、ログを有効にする **サービス** を選択します。

   > [!div class="mx-imgBorder"]
   > ![Azure Portal でログを構成する。](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. **[データの削除]** チェック ボックスがオンになっていることを確認します。  次に、チェック ボックスの下にあるスライダー コントロールを動かすか、スライダー コントロールの横にあるテキスト ボックスに表示される値を直接変更して、ログ データを保持する日数を設定します。 新しいストレージ アカウントの既定値は 7 日間です。 保有ポリシーを設定しない場合は、「0」(ゼロ) を入力します。 保持ポリシーがない場合、ログ データを削除する責任はユーザーが負います。

   > [!WARNING]
   > ログは、アカウントにデータとして格納されます。 ログ データは時間の経過と共にアカウントに蓄積されるため、ストレージのコストが増加するおそれがあります。 ログ データが必要なのが短期間のみである場合は、データ保持ポリシーを変更することでコストを削減できます。 古いログ データ (保持ポリシーよりも古いデータ) はシステムによって削除されます。 アカウントのログ データを保持する必要がある期間に基づいて、保持ポリシーを設定することをお勧めします。 詳細については、「[Billing on storage metrics](storage-analytics-metrics.md#billing-on-storage-metrics)」 (ストレージ メトリックへの課金) を参照してください。

4. **[保存]** をクリックします。

   診断ログは、ストレージ アカウントの *$logs* という名前の BLOB コンテナーに保存されます。 ログ データを表示するには、[Microsoft Azure Storage Explorer](https://storageexplorer.com) などのストレージ エクスプローラーを使用するか、プログラムによってストレージ クライアント ライブラリまたは PowerShell を使用します。

   $Logs コンテナーへのアクセスについては、[ストレージ分析ログ](storage-analytics-logging.md)に関するページを参照してください。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell コマンド ウィンドウを開きます。

2. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

3. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

5. 使用するストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

   * `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。 

6. **Set-AzStorageServiceLoggingProperty** を使用して現在のログ設定を変更します。 ストレージ ログを制御するコマンドレットでは、**LoggingOperations** パラメーターが使用されます。このパラメーターは文字列で、記録する要求の種類がコンマ区切り形式で含まれています。 指定できる要求の種類には、**read**、**write**、および **delete** の 3 つがあります。 ログをオフにするには、**LoggingOperations** パラメーターに **none** の値を指定します。  

   以下のコマンドでは、既定のストレージ アカウント内の Queue サービスで read、write、および delete の各要求に対するログがオンにされます。リテンション期間は 5 日間に設定されています。  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > ログは、アカウントにデータとして格納されます。 ログ データは時間の経過と共にアカウントに蓄積されるため、ストレージのコストが増加するおそれがあります。 ログ データが必要なのが短期間のみである場合は、データ保持ポリシーを変更することでコストを削減できます。 古いログ データ (保持ポリシーよりも古いデータ) はシステムによって削除されます。 アカウントのログ データを保持する必要がある期間に基づいて、保持ポリシーを設定することをお勧めします。 詳細については、「[Billing on storage metrics](storage-analytics-metrics.md#billing-on-storage-metrics)」 (ストレージ メトリックへの課金) を参照してください。
   
   以下のコマンドでは、既定のストレージ アカウント内の Table サービスに対するログがオフにされます。  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Azure サブスクリプションを処理するように Azure PowerShell コマンドレットを構成する方法と、使用する既定のストレージ アカウントを選択する方法については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/)に関する記事をご覧ください。  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>ログ データ保持期間を変更する

ログ データは時間の経過と共にアカウントに蓄積されるため、ストレージのコストが増加するおそれがあります。 ログ データが必要なのが短期間のみである場合は、ログ データ保持期間を変更することでコストを削減できます。 たとえば、ログが 3 日間だけ必要な場合は、ログ データ保持期間を `3` の値に設定します。 これにより、ログは 3 日後にアカウントから自動的に削除されます。 このセクションでは、現在のログ データ保持期間を確認し、必要に応じてその期間を更新する方法について説明します。

> [!NOTE]
> これらの手順は、 **[Hierarchical namespace]\(階層構造の名前空間\)** の設定が有効になっていないアカウントにのみ適用されます。 アカウントでこの設定を有効にしている場合、保持日数の設定はまだサポートされていません。 代わりに、Azure Storage Explorer、REST、SDK などのサポートされているツールを使用して、ログを手動で削除する必要があります。 ストレージ アカウント内でそれらのログを確認するには、「[ログの保存方法](storage-analytics-logging.md#how-logs-are-stored)」を参照してください。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com) で **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択して、ストレージ アカウント ブレードを開きます。
2. メニュー ブレードの **[監視 (クラシック)]** セクションで **[診断設定 (クラシック)]** を選択します。

    ![Azure portal の [監視] の [診断] メニュー項目](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. **[データの削除]** チェック ボックスがオンになっていることを確認します。  次に、チェック ボックスの下にあるスライダー コントロールを動かすか、スライダー コントロールの横にあるテキスト ボックスに表示される値を直接変更して、ログ データを保持する日数を設定します。

   > [!div class="mx-imgBorder"]
   > ![Azure portal で保持期間を変更する](./media/manage-storage-analytics-logs/modify-retention-period.png)

   新しいストレージ アカウントの既定の日数は 7 日間です。 保有ポリシーを設定しない場合は、「0」(ゼロ) を入力します。 保有ポリシーがない場合、監視データを削除する責任はユーザーが負います。
   
4. **[保存]** をクリックします。

   診断ログは、ストレージ アカウントの *$logs* という名前の BLOB コンテナーに保存されます。 ログ データを表示するには、[Microsoft Azure Storage Explorer](https://storageexplorer.com) などのストレージ エクスプローラーを使用するか、プログラムによってストレージ クライアント ライブラリまたは PowerShell を使用します。

   $Logs コンテナーへのアクセスについては、[ストレージ分析ログ](storage-analytics-logging.md)に関するページを参照してください。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell コマンド ウィンドウを開きます。

2. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

3. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

5. ストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

   * `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。 

6. [Get-AzStorageServiceLoggingProperty](/powershell/module/az.storage/get-azstorageserviceloggingproperty) を使用して現在のログ保持ポリシーを表示します。 次の例では、BLOB とキューのストレージ サービスの保持期間をコンソールに出力します。

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   コンソール出力では、保持期間は `RetentionDays` という列見出しの下に表示されます。

   > [!div class="mx-imgBorder"]
   > ![PowerShell の出力の保持ポリシー](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. [Set-AzStorageServiceLoggingProperty](/powershell/module/az.storage/set-azstorageserviceloggingproperty) を使用して保持期間を変更します。 次の例では、保持期間を 4 日に変更します。  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Azure サブスクリプションを処理するように Azure PowerShell コマンドレットを構成する方法と、使用する既定のストレージ アカウントを選択する方法については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/)に関する記事をご覧ください。  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

次の例では、BLOB とキューのストレージ サービスの保持期間をコンソールに出力します。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

次の例では、保持期間を 4 日に変更します。 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

次の例では、BLOB とキューのストレージ サービスの保持期間をコンソールに出力します。

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

次の例では、BLOB とキューのストレージ サービスのログの保持期間を 4 日に変更します。 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>ログ データが削除されていることを確認する

ストレージ アカウントの `$logs` コンテナーの内容を表示することで、ログが削除されていることを確認できます。 次の図は、`$logs` コンテナー内のフォルダーの内容を示しています。 このフォルダーは 2021 年 1 月に対応し、各フォルダーには 1 日分のログが含まれています。 今日が 2021 年 1 月 29 日であり、保持ポリシーが 1 日だけに設定されている場合、このフォルダーには 1 日だけのログが含まれています。

> [!div class="mx-imgBorder"]
> ![Azure Portal のログ フォルダーの一覧](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>ログ データを表示する

 ログ データを表示して分析するには、対象のログ データを含む BLOB をローカル マシンにダウンロードする必要があります。 多くのストレージ閲覧ツールを使用して、ストレージ アカウントから BLOB をダウンロードできます。また、Azure Storage チームが提供するコマンドライン用の Azure Copy Tool ([AzCopy](storage-use-azcopy-v10.md)) を使用して、ログ データをダウンロードすることもできます。  
 
>[!NOTE]
> `$logs` コンテナーは Event Grid と統合されていないため、ログ ファイルが書き込まれたときにユーザーは通知を受信しません。 

 対象のログ データをダウンロードし、同じログ データを複数回ダウンロードしないためには、以下のようにします。  

-   同じデータを複数回ダウンロードしないようにするには、ログ データを含む BLOB に対して日付と時刻の名前付け規則を使用し、既にダウンロードしている分析対象の BLOB を追跡します。  

-   ダウンロードする必要がある BLOB を正確に識別するには、ログ データを含む BLOB のメタデータを使用して、BLOB がログ データを保持する期間を確認します。  

AzCopy の使用を開始するには、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」を参照してください。 

次の例は、2014 年 5 月 20 日の午前 09 時、午前 10 時、および午前 11 時から Queue サービスのログ データをダウンロードする方法を示しています。

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

特定のファイルをダウンロードする方法の詳細については、「[AzCopy v10 を使用して Azure BLOB ストレージから BLOB をダウンロードする](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

ログ データのダウンロードが完了すると、ファイル内のログ エントリを表示できます。 これらのログ ファイルでは、多くのログ読み取りツールで解析できる区切り記号付きテキスト形式が使用されています (詳細については、「[Microsoft Azure Storage の監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)」を参照してください)。 ログ ファイルの内容を書式設定、フィルタリング、並べ替え、AD 検索するために、各種のツールがさまざまな機能を提供しています。 ストレージ ログのログ ファイルの形式および内容の詳細については、「[Storage Analytics Log Format (Storage Analytics のログ形式)](/rest/api/storageservices/storage-analytics-log-format)」および「[Storage Analytics Logged Operations and Status Message (Storage Analytics によって記録される操作および状態メッセージ)](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)」を参照してください。

## <a name="next-steps"></a>次のステップ

* Storage Analytics の詳細については、Storage Analytics の「[Storage Analytics](storage-analytics.md)」を参照してください。
* .NET 言語を使用してストレージ ログを構成する方法の詳細については、「[Storage Client Library Reference (ストレージ クライアント ライブラリ リファレンス)](/previous-versions/azure/dn261237(v=azure.100))」を参照してください。 
* REST API を使用してストレージ ログを構成する方法の概要については、「[Enabling and Configuring Storage Analytics (Storage Analytics の有効化および構成)](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)」を参照してください。
* Storage Analytics ログの形式の詳細について学習します。 「[Storage Analytics のログの形式](/rest/api/storageservices/storage-analytics-log-format)」を参照してください。
