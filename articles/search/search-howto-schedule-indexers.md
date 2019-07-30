---
title: インデクサーのスケジュールを設定する方法 - Azure Search
description: Azure Search インデクサーのスケジュールを設定して、定期的に、または特定の時間にコンテンツのインデックスを作成します。
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 4bf931b19b7490a94f30afde49038cdc7573fab3
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302246"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Azure Search のインデクサーのスケジュールを設定する方法
インデクサーは通常、その作成直後に 1 回実行されます。 必要に応じて、ポータル、REST API、または .NET SDK を使用してもう一度実行できます。 スケジュールに従って定期的に実行するようにインデクサーを構成することもできます。

インデクサーのスケジュール設定が役立つ状況には、次のものがあります。

* ソース データが時間の経過と共に変化し、変更されたデータを Azure Search インデクサーによって自動的に処理したい。
* 複数のデータ ソースからインデックスが取得され、競合を減らすために、必ず異なるタイミングでインデクサーが実行されるようにしたい。
* ソース データが非常に大きく、時間の経過と共にインデクサーの処理を拡大したい。 大量のデータのインデックス作成の詳細については、「[Azure Search で大容量のデータ セットのインデックスを作成する方法](search-howto-large-index.md)」を参照してください。

スケジューラは Azure Search の組み込みの機能です。 外部のスケジューラを使用して検索インデクサーを制御することはできません。

## <a name="define-schedule-properties"></a>スケジュールのプロパティを定義する

インデクサーのスケジュールには、2 つのプロパティがあります。
* **[Interval] \(間隔\)** : スケジュールが設定されたインデクサーの実行間隔を定義します。 設定できる最短の間隔は 5 分、最長は 24 時間です。
* **[Start Time (UTC)] \(開始時刻 (UTC)\)** : インデクサーを実行する最初の時刻を示します。

最初にインデクサーを作成するときに、または後でインデクサーのプロパティを更新することによってスケジュールを指定できます。 インデクサーのスケジュールは、[ポータル](#portal)、[REST API](#restApi)、または [.NET SDK](#dotNetSdk) を使用して設定できます。

特定のインデクサーを実行できるのは一度に 1 つだけです。 インデクサーの次の開始予定時間にそのインデクサーが既に実行されている場合、その実行は、次回予定されている時間まで延期されます。

さらに具体的な例を示します。 **[Interval] \(間隔\)** を 1 時間、 **[Start Time] \(開始時刻\)** を 2019 年 6 月 1 日午前 8 時 0 分 0 秒 (UTC) にしてインデクサーのスケジュールを構成するとします。 インデクサーの実行に 1 時間より長くかかると、次のことが起きる可能性があります。

* 最初のインデクサーの実行は、2019 年 6 月 1 日午前 8 時 (UTC) 前後に開始します。 この実行に 20 分 (または、1 時間未満) かかるものとします。
* 2 番目の実行は、2019 年 6 月 1 日午前 9 時 (UTC) 前後に開始します。 この実行に 70 分 (1 時間より長く) かかり、午前 10 時 10 分 (UTC) まで完了しないとします。
* 3 回目の実行は午前 10 時 (UTC) に開始するようにスケジュール設定されていますが、その時点で前の実行がまだ終わっていません。 このとき、このスケジュール設定された実行はスキップされます。 インデクサーの次の実行は午前 11 時 (UTC) まで開始しません。

> [!NOTE]
> インデクサーが特定のスケジュールに設定されているが実行のたびに同じドキュメントに対して繰り返し失敗する場合、進捗が再び正常化するまでの間、インデクサーは (最大で 24 時間に 1 回に) 間隔を開けて実行頻度を下げます。  インデクサーが特定の箇所で停止する原因になっていた問題をすべて修正したと思われる場合、インデクサーをオンデマンドで実行できます。それによって進捗が正常になったら、インデクサーは設定されていたスケジュール間隔に復帰します。

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>ポータルでスケジュールを定義する

ポータルでデータ インポート ウィザードを使用して、インデクサーのスケジュールを作成時に定義できます。 既定のスケジュール設定は **[Hourly] \(毎時間\)** で、この場合、インデクサーは作成後に 1 回実行され、その後は 1 時間ごとに再実行されます。

インデクサーを自動的に再実行したくない場合、スケジュール設定を **[Once] \(1 回\)** に変更できます。または、1 日に 1 回実行する場合は **[Daily] \(毎日\)** に変更できます。 異なる間隔または将来の特定の開始時刻を指定したい場合、 **[カスタム]** に設定します。

スケジュールを **[カスタム]** に設定すると、 **[Interval] \(間隔\)** と **[Start Time (UTC)] \(開始時刻 (UTC)\)** を指定するフィールドが表示されます。 設定できる最短の間隔は 5 分、最長は 1440 分 (24 時間) です。

   ![データ インポート ウィザードでのインデクサーのスケジュールの設定](media/search-howto-schedule-indexers/schedule-import-data.png "データ インポート ウィザードでのインデクサーのスケジュールの設定")

インデクサーが作成されたら、インデクサーの編集パネルを使用してスケジュール設定を変更できます。 スケジュールのフィールドはデータ インポート ウィザードと同じです。

   ![インデクサーの編集パネルでのスケジュールの設定](media/search-howto-schedule-indexers/schedule-edit.png "インデクサーの編集パネルでのスケジュールの設定")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>REST API を使用してスケジュールを定義する

REST API を使用してインデクサーのスケジュールを定義できます。 そのためには、インデクサーを作成または更新するときに、**schedule** プロパティを含めます。 次の例では、既存のインデクサーを更新する PUT 要求を示します。

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**interval** パラメーターは必須です。 interval は、連続する 2 つのインデクサー実行の開始の時間間隔を示します。 許可される最短の間隔は 5 分です。最長は 1 日です。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 使用されるパターンは、`P(nD)(T(nH)(nM))` です。 たとえば、15 分ごとの場合は `PT15M`、2 時間ごとの場合は `PT2H` です。

省略可能な **startTime** は、スケジュールされた実行が開始する時刻を示します。 省略すると、現在の UTC 時刻が使用されます。 この時刻は過去でもかまいません。その場合、最初の実行はインデクサーが元の **startTime** から継続的に実行されているかのようにスケジュールされます。

[インデクサーを実行] 呼び出しを使用して、いつでも必要に応じてインデクサーを実行することもできます。 インデクサーの実行とインデクサーのスケジュール設定の詳細については、[インデクサーの実行](https://docs.microsoft.com/rest/api/searchservice/run-indexer)、[インデクサーの取得](https://docs.microsoft.com/rest/api/searchservice/get-indexer)、[インデクサーの更新](https://docs.microsoft.com/rest/api/searchservice/update-indexer)に関する REST API リファレンスの項目を参照してください。

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>.NET SDK を使用してスケジュールを定義する

Azure Search .NET SDK を使用してインデクサーのスケジュールを定義できます。 そのためには、インデクサーを作成または更新するときに、**schedule** プロパティを含めます。

次の C# の例では、定義済みのデータ ソースとインデックスを使用してインデクサーを作成し、初回は今から 30 分後、その後は毎日 1 回実行するようにスケジュールを設定します。

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
**schedule** パラメーターを省略した場合、インデクサーは作成直後に 1 回だけ実行されます。

**startTime** パラメーターは過去の時間に設定できます。 その場合、最初の実行は、指定された **startTime** から継続的にインデクサーが実行されているかのようにスケジュールされます。

スケジュールは [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) クラスを使用して定義されます。 **IndexingSchedule** コンストラクターでは、**TimeSpan** オブジェクトを使用して **interval** パラメーターを指定する必要があります。 設定できる最短の間隔値は 5 分、最長は 24 時間です。 **DateTimeOffset** オブジェクトとして指定された 2 番目の **startTime** パラメーターは省略可能です。

.NET SDK では、[SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) クラスとその [Indexers](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) プロパティを使用してインデクサーの操作を制御できます。これは、**IIndexersOperations** インターフェイスのメソッドを実装します。 

[Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run)、[RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)、または [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) メソッドのいずれかを使用して、いつでも必要に応じてインデクサーを実行できます。

インデクサーの作成、更新、および実行の詳細については、[IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet) を参照してください。
