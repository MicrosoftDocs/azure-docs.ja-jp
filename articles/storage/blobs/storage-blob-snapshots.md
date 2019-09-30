---
title: Azure Storage での BLOB の読み取り専用スナップショットの作成 | Microsoft Docs
description: BLOB のスナップショットを作成して、特定の時点での BLOB データをバックアップする方法について説明します。 スナップショットの課金方法と、スナップショットを使用して容量使用料金を最小限に抑える方法を理解します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 0da3373ba2c13bd6a00a92a6b38bead86fc9a5ea
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897009"
---
# <a name="create-a-blob-snapshot"></a>BLOB のスナップショットの作成

スナップショットは、ある時点で作成された読み取り専用の BLOB です。 スナップショットは BLOB をバックアップするのに便利です。 作成したスナップショットの読み取り、コピー、削除はできますが、変更はできません。

スナップショットが作成された日時を示す **DateTime** 値が BLOB の URI に追加される点を除き、BLOB のスナップショットはベース BLOB とまったく同じです。 たとえば、ページ BLOB の URI が `http://storagesample.core.blob.windows.net/mydrives/myvhd` の場合、スナップショットの URI は `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` のようになります。

> [!NOTE]
> すべてのスナップショットがベース BLOB の URI を共有します。 ベース BLOB とスナップショットの唯一の違いは、追加される **DateTime** 値です。
>

BLOB に対するスナップショットの数に制限はありません。 スナップショットは、明示的に削除されるまで保持されます。 スナップショットは、ベース BLOB よりも長く保持することはできません。 ベース BLOB に関連付けられたスナップショットを列挙して、現在のスナップショットを追跡できます。

BLOB のスナップショットを作成すると、BLOB のシステム プロパティが同じ値でスナップショットにコピーされます。 また、スナップショットの作成時に別のメタデータを指定しない限り、ベース BLOB のメタデータもスナップショットにコピーされます。

ベース BLOB に関連付けられているリースはスナップショットにコピーされません。 スナップショットはリースを取得することはできません。

VHD ファイルは、VM ディスクの現時点の情報と状態の格納に使用します。 ディスクを VM 内から切断するか、VM をシャットダウンしてから、その VHD ファイルのスナップショットを撮ることができます。 このスナップショットを後に使用して、その時点での VHD ファイルを取得して VM を再作成することができます。

## <a name="create-a-snapshot"></a>スナップショットの作成
次のコード例は、[.NET 用 Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage/client)を使用して、スナップショットを作成する方法を示しています。 この例では、スナップショットの作成時に別のメタデータを指定しています。

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="copy-snapshots"></a>スナップショットのコピー
BLOB やスナップショットに関連するコピー操作は次のルールに従います。

* スナップショットをベース BLOB にコピーします。 スナップショットをベース BLOB に昇格することにより、BLOB を以前のバージョンに復元できます。 スナップショットは元のままですが、ベース BLOB はスナップショットの書き込み可能なコピーによって上書きされます。
* スナップショットを別の名前でコピー先 BLOB にコピーします。 結果として得られるコピー先 BLOB は書き込み可能な BLOB であり、スナップショットではありません。
* コピー元 BLOB をコピーしても、コピー元 BLOB のスナップショットはコピー先にコピーされません。 コピー先 BLOB をコピーで上書きしても、元のコピー先 BLOB に関連付けられたスナップショットは元の状態のままになります。
* ブロック BLOB のスナップショットを作成する場合、BLOB のコミットされたブロック リストもスナップショットにコピーされます。 コミット前のブロックはコピーされません。

## <a name="specify-an-access-condition"></a>アクセス条件の指定
[CreateSnapshotAsync][dotnet_CreateSnapshotAsync] を呼び出すとき、アクセス条件を指定することで、その条件を満たしている場合にのみスナップショットを作成できます。 アクセス条件を指定するには、[AccessCondition][dotnet_AccessCondition] パラメーターを使用します。 指定した条件を満たしていない場合、スナップショットは作成されず、Blob service では状態コード [HTTPStatusCode][dotnet_HTTPStatusCode].PreconditionFailed が返されます。

## <a name="delete-snapshots"></a>スナップショットの削除
スナップショットを持つ BLOB は、スナップショットも削除しないと削除できません。 スナップショットは個別に削除することも、コピー元 BLOB が削除されたときにすべてのスナップショットを削除するように指定することもできます。 スナップショットがまだ存在する BLOB を削除しようとすると、エラーが発生します。

次のコード例は、.NET で BLOB とそのスナップショットを削除する方法を示しています。`blockBlob` は [CloudBlockBlob][dotnet_CloudBlockBlob] 型のオブジェクトです。

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Azure Premium Storage のスナップショット
Premium Storage でスナップショットを使用するには、次のルールに従います。

* Premium Storage アカウントにおけるページ BLOB あたりのスナップショットの最大数は 100 です。 上限を超えると、スナップショット BLOB の操作でエラー コード 409 (`SnapshotCountExceeded`) が返されます。
* Premium Storage アカウントのページ BLOB のスナップショットは 10 分ごとに作成できます。 上限を超えると、スナップショット BLOB の操作でエラー コード 409 (`SnapshotOperationRateExceeded`) が返されます。
* スナップショットを読み込むには、Copy Blob を操作してアカウントの別のページ BLOB にスナップショットをコピーします。 すでにスナップショットがある BLOB にはスナップショットをコピーできません。 コピー先の BLOB にスナップショットがある場合、Copy Blob 操作でエラー コード 409 (`SnapshotsPresent`) が返されます。

## <a name="return-the-absolute-uri-to-a-snapshot"></a>スナップショットに絶対 URI を返す
この C# コードの例では、スナップショットを作成し、プライマリの場所の絶対 URI を書き込みます。

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>スナップショットの課金方法について
スナップショット (BLOB の読み取り専用コピー) を作成すると、別途データ ストレージ料金がアカウントに課金される場合があります。 不要なコストを抑えるためにも、アプリケーションを設計する際は、この料金が発生するしくみを理解しておくことが重要です。

### <a name="important-billing-considerations"></a>課金に関する重要な考慮事項
スナップショットの作成時に考慮する必要のある重要なポイントを以下に示します。

* ストレージ アカウントには、一意のブロックまたはページに対する料金が発生します。そのブロックまたはページが BLOB とスナップショットのどちらに含まれているかは関係ありません。 BLOB に関連付けられているスナップショットについてアカウントに追加料金が発生するのは、そのベースになっている BLOB を更新したときです。 ベース BLOB を更新すると、BLOB はスナップショットから分化します。 この場合、各 BLOB またはスナップショットの一意のブロックまたはページに対して課金されます。
* ブロック BLOB 内のブロックを置き換えた場合、以後そのブロックは一意のブロックとして課金されます。 そのブロックに割り当てられているブロック ID とデータが、スナップショット側と同じであっても変わりません。 ブロックが再度コミットされると、そのブロックはスナップショット内の対応するブロックから分化し、そのデータに対する料金が発生します。 これは、まったく同じデータでページ BLOB 内のページを更新した場合にも当てはまります。
* [UploadFromFile][dotnet_UploadFromFile]、[UploadText][dotnet_UploadText]、[UploadFromStream][dotnet_UploadFromStream]、[UploadFromByteArray][dotnet_UploadFromByteArray] のいずれかのメソッドを呼び出してブロック BLOB を置き換えると、その BLOB 内のすべてのブロックが置き換えられます。 その BLOB に関連付けられているスナップショットがある場合、ベース BLOB とスナップショットのすべてのブロックが分化し、両方の BLOB のすべてのブロックが課金対象となります。 これは、ベース BLOB 内のデータとスナップショット内のデータとがまったく同一であっても変わりません。
* 2 つのブロックに格納されているデータが同一であるかどうかを判断する方法は、Azure Blob service には用意されていません。 アップロードされてコミットされたブロックは、同じデータや同じブロック ID がある場合でも、それぞれが一意のものとして扱われます。 料金は一意のブロックに対して発生するため、スナップショットがある BLOB を更新すると、一意のブロックが増え、追加料金が発生することを考慮することが重要です。

### <a name="minimize-cost-with-snapshot-management"></a>スナップショット管理によりコストを最小限に抑える

不要な料金を回避するために、スナップショットを慎重に管理することをお勧めします。 次のベスト プラクティスに従えば、スナップショットの記憶域から発生するコストを最小限に抑えられます。

* まったく同じデータで更新する場合も含め、BLOB を更新するときは必ずその BLOB に関連付けられているスナップショットを削除してから作成し直すようにします (アプリケーションの設計上、スナップショットを維持しなければならない場合を除く)。 BLOB のスナップショットを削除してから作成し直すことにより、BLOB とスナップショットの分化を確実に防ぐことができます。
* BLOB のスナップショットを維持する場合は、[UploadFromFile][dotnet_UploadFromFile]、[UploadText][dotnet_UploadText]、[UploadFromStream][dotnet_UploadFromStream]、または [UploadFromByteArray][dotnet_UploadFromByteArray] の呼び出しによる BLOB の更新を行わないようにします。 これらのメソッドを呼び出すと、BLOB 内のすべてのブロックが置き換えられ、ベース BLOB とスナップショットが大幅に分化します。 代わりに、[PutBlock][dotnet_PutBlock] メソッドと [PutBlockList][dotnet_PutBlockList] メソッドを使用して、最小数のブロックのみを更新するようにしてください。

### <a name="snapshot-billing-scenarios"></a>スナップショットの課金シナリオ
ブロック BLOB とそのスナップショットについて料金が発生するしくみを次のシナリオで説明します。

**シナリオ 1**

シナリオ 1 では、スナップショットが作成されてからベース BLOB は更新されていません。したがって、料金の発生対象は、一意のブロックである 1、2、3 のみです。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**シナリオ 2**

シナリオ 2 では、ベース BLOB は更新されていますが、スナップショットは更新されていません。 更新されたのはブロック 3 です。格納されているデータと ID が同じでも、スナップショット内のブロック 3 とは異なります。 このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**シナリオ 3**

シナリオ 3 では、ベース BLOB は更新されていますが、スナップショットは更新されていません。 ベース BLOB のブロック 3 がブロック 4 に置き換わっていますが、スナップショット側はブロック 3 のままです。 このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**シナリオ 4**

シナリオ 4 では、ベース BLOB が完全に更新されており、元のブロックは 1 つも含まれていません。 このため、8 つある一意のブロックすべてについての料金がアカウントに課金されます。 このシナリオは、[UploadFromFile][dotnet_UploadFromFile]、[UploadText][dotnet_UploadText]、[UploadFromStream][dotnet_UploadFromStream]、[UploadFromByteArray][dotnet_UploadFromByteArray] などの更新メソッド を使用した場合に発生する可能性があります。これは、これらのメソッドによって BLOB の内容がすべて置き換えられるためです。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>次の手順

* 仮想マシン (VM) ディスク スナップショットの操作の詳細については、「[増分スナップショットを使用した Azure 非管理 VM ディスクのバックアップ](../../virtual-machines/windows/incremental-snapshots.md)」を参照してください。

* BLOB ストレージのその他の使用例については、「[Azure のコード サンプル](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)」を参照してください。 サンプル アプリケーションをダウンロードして実行することも、GitHub でコードを参照することもできます。

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext