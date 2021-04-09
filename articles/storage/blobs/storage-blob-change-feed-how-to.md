---
title: Azure Blob Storage の変更フィードを処理する | Microsoft Docs
description: .NET クライアント アプリケーションで変更フィード ログを処理する方法を学習します
author: normesta
ms.author: normesta
ms.date: 09/08/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: f0e89fdfba852fc056cf48efd1b92daabb272cf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89568253"
---
# <a name="process-change-feed-in-azure-blob-storage"></a>Azure Blob Storage の変更フィードを処理する

変更フィードでは、ストレージ アカウント内の BLOB と BLOB メタデータに対して行われるすべての変更のトランザクション ログが提供されます。 この記事では、BLOB 変更フィード プロセッサ ライブラリを使用して、変更フィード レコードを読み取る方法を示します。

変更フィードの詳細については、[Azure Blob Storage の変更フィード](storage-blob-change-feed.md)に関するページを参照してください。

## <a name="get-the-blob-change-feed-processor-library"></a>BLOB 変更フィード プロセッサ ライブラリを取得する

1. コマンド ウィンドウを開きます (例: Windows PowerShell)。
2. ご利用のプロジェクト ディレクトリから、[NuGet パッケージ **Azure.Storage.Blobs.Changefeed**](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/) をインストールします。

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>レコードの読み取り

> [!NOTE]
> 変更フィードは、ストレージ アカウントの不変および読み取り専用のエンティティです。 任意の数のアプリケーションで、変更フィードの読み取りと処理を都合のよいときに同時に個別に行うことができます。 アプリケーションでの読み取り時に、変更フィードからレコードが削除されることはありません。 使用する各リーダーの読み取りまたは反復処理の状態は独立しており、アプリケーションによってのみ維持されます。

この例では、変更フィードですべてのレコードを反復処理し、それらをリストに追加してから、そのリストを呼び出し元に返します。
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

この例では、リスト内の各レコードからのいくつかの値をコンソールに出力します。 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>保存位置からレコードの読み取りを再開する

変更フィードに自分の読み取り位置を保存してから、後でレコードの反復処理を再開するように選択できます。 読み取り位置を保存するには、変更フィード カーソルを取得します。 カーソルは **文字列** であり、アプリケーションでは、アプリケーションの設計に適した方法でその文字列を保存できます (たとえば、ファイルまたはデータベースへ)。

この例では、変更フィード内のすべてのレコードを反復処理し、それらをリストに追加し、カーソルを保存します。 リストとカーソルは呼び出し元に返されます。 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>レコードのストリーム処理

変更フィード レコードが変更フィードにコミットされたときにそれらを処理するように選択できます。 「[仕様](storage-blob-change-feed.md#specifications)」を参照してください。 変更イベントは、平均 60 秒の期間に変更フィードに発行されます。 ポーリング間隔を指定する場合は、この期間を考慮して新しい変更をポーリングすることをお勧めします。

この例では、変更を定期的にポーリングします。  変更レコードが存在する場合、このコードではそれらのレコードが処理され、変更フィード カーソルが保存されます。 このようにすると、プロセスがいったん停止されてから再び開始される場合、アプリケーションでカーソルが使用され、最後に中断した箇所のレコードの処理が再開されます。 この例では、ローカル アプリケーション構成ファイルにカーソルが保存されますが、自分のアプリケーションでは、自分のシナリオに最も適した形式で保存することができます。 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>時間範囲内のレコードの読み取り

特定の時間範囲内のレコードを読み取ることができます。 この例では、変更フィード内の 2020 年 3 月 2 日午後 3:00 時から 2020 年 8 月 7 日午前 2:00 時までのすべてのレコードを反復処理し、それらをリストに追加してから、そのリストを呼び出し元に返します。

### <a name="selecting-segments-for-a-time-range"></a>時間範囲のセグメントの選択

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

指定した開始時刻は、最も近い時間に切り捨てられ、終了時刻は最も近い時間に切り上げられます。 開始時刻の前と終了時刻の後に発生したイベントがユーザーに表示される可能性があります。 また、開始時刻と終了時刻の間に発生する一部のイベントが表示されない場合もあります。 これは、イベントが開始時刻の前の時間中または終了時刻の後の時間中に記録される可能性があるためです。

## <a name="next-steps"></a>次のステップ

変更フィード ログについて、さらに学習します。 [Azure Blob Storage の変更フィード](storage-blob-change-feed.md) に関するページを参照してください。
