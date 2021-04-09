---
title: Azure ページ BLOB の概要 |Microsoft Docs
description: サンプル スクリプトでのユース ケースを含む、Azure ページ BLOB とその利点の概要。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: aada418b4f74c38a2a35c793deb85b94b703fb89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629359"
---
# <a name="overview-of-azure-page-blobs"></a>Azure ページ BLOB の概要

Azure Storage には、3 つの種類の BLOB ストレージが用意されています。ブロック BLOB、追加 BLOB、およびページ BLOB です。 ブロック BLOB はブロックで構成され、テキストまたはバイナリ ファイルの格納と大きいファイルの効率的なアップロードに最適です。 追加 BLOB もブロックで構成されますが、追加操作用に最適化されているので、ログ記録シナリオに最適です。 ページ BLOB は 512 バイトのページ (合計で最大 8 TB) で構成され、頻繁なランダムの読み取り/書き込み操作のために設計されています。 ページ BLOB は、Azure IaaS ディスクの基盤です。 この記事では、ページ BLOB の機能とメリットに重点を置いて説明します。

ページ BLOB は 512 バイトのページの集まりであり、任意の範囲のバイトの読み取り/書き込みを行うことができます。 そのため、ページ BLOB は、仮想マシンとデータベースの OS やデータ ディスクのような、インデックスに基づくスパース データ構造の格納に最適です。 たとえば、Azure SQL DB は、そのデータベースの基になる永続的ストレージとしてページ BLOB を使用します。 さらに、ページ BLOB は、範囲を基にした更新を含むファイルにもよく使用されます。  

Azure のページ BLOB の重要な機能は、その REST インターフェイス、基礎をなすストレージの耐久性、および Azure へのシームレスな移行機能です。 これらの機能については、次のセクションで詳しく説明します。 さらに、Azure のページ BLOB は、現時点では次の 2 種類のストレージでサポートされています。Premium Storage と Standard Storage。 Premium Storage は、特に一貫したハイ パフォーマンスと待機時間の短縮を必要とするワークロード向けに設計されているため、Premium ページ BLOB をハイ パフォーマンスのストレージ シナリオに最適なものにします。 Standard ストレージ アカウントは、待機時間の影響を受けないワークロードを実行する場合はコスト効率に優れています。

## <a name="restrictions"></a>制限

ページ BLOB で使用できるのは、**ホット** アクセス層のみであり、**クール** または **アーカイブ** 層を使用することはできません。 アクセス層の詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](storage-blob-storage-tiers.md)」を参照してください。

## <a name="sample-use-cases"></a>サンプル ユース ケース

ページ BLOB のいくつかのユース ケースについて、Azure IaaS ディスクから説明します。 Azure のページ BLOB は、Azure IaaS の仮想ディスクのプラットフォームのバックボーンです。 Azure の OS とデータ ディスクは両方とも仮想ディスクとして実装され、そこでデータは Azure Storage プラットフォームで永続化され、その後、パフォーマンスを最大にするために仮想マシンに配信されます。 Azure ディスクは HYPER-V [VHD 形式](/previous-versions/windows/it-pro/windows-7/dd979539(v=ws.10))で永続化され、Azure Storage に[ページ BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) として格納されます。 ページ BLOB は、Azure IaaS VM の仮想ディスクを使用するだけでなく、現時点で SQL データの格納のためにページ BLOB を使用している Azure SQL DB サービスなどの PaaS と DBaaS のシナリオも実現して、データベースの高速でランダムな読み取り/書き込み操作を可能にします。 もう 1 つの例として、コラボレーション用のビデオ編集アプリケーションの共有メディアへのアクセスに PaaS サービスを使用している場合は、ページ BLOB によってメディア内のランダムな場所への高速アクセスが可能になります。 また、複数のユーザーが同じメディアを高速かつ効率的に編集したりマージしたりできるようにもなります。 

Azure Site Recovery、Azure Backup のようなファースト パーティの Microsoft サービスと、多くのサード パーティの開発者は、ページ BLOB の REST インターフェイスを使用して、業界をリードするイノベーションを実装しています。 Azure に実装されている固有のシナリオの一部を、次に示します。 

* アプリケーションをターゲットにした増分スナップショットの管理:アプリケーションは、ページ BLOB のスナップショットと REST API を利用して、コストがかかるデータの重複を発生させずに、アプリケーションのチェックポイントを保存できます。 Azure Storage では、ページ BLOB のローカル スナップショットがサポートされています。このスナップショットは、BLOB 全体のコピーを必要としません。 これらのパブリック スナップショット API では、スナップショット間でのデルタへのアクセスとコピーも可能です。
* オンプレミスからクラウドへのアプリケーションとデータのライブ マイグレーション:オンプレミスの VM の実行を続けながら、オンプレミス データをコピーし、REST API を使用して Azure のページ BLOB に直接書き込みます。 ターゲットが書き込まれたら、そのデータを使用して Azure VM にすばやくフェールオーバーできます。 このようにして、最小限のダウンタイムで VM と仮想ディスクをオンプレミスからクラウドに移行できます。これは、VM の使用を続けている間にバックグラウンドでデータの移行が行われて、フェールオーバーに必要なダウンタイムが短縮されるためです (数分)。
* [SAS ベース](../common/storage-sas-overview.md)の共有アクセス。コンカレンシー制御のサポートによる複数のリーダーと単一のライターのようなシナリオを実現できます。

## <a name="pricing"></a>価格

ページ BLOB で提供されるストレージの種類には、それぞれ独自の価格モデルがあります。 Premium ページ BLOB はマネージド ディスクの価格モデルに従いますが、標準ページ BLOB は、使用されたサイズと各トランザクションに対して課金されます。 詳細については、[Azure ページ BLOB の価格ページ](https://azure.microsoft.com/pricing/details/storage/page-blobs/)を参照してください。

## <a name="page-blob-features"></a>ページ BLOB の機能

### <a name="rest-api"></a>REST API

[ページ BLOB を使用した開発](./storage-quickstart-blobs-dotnet.md)の概要については、次のドキュメントを参照してください。 例として、.NET 用ストレージ クライアント ライブラリを使用してページ BLOB にアクセスする方法について見てみましょう。 

次の図は、アカウント、コンテナー、およびページ BLOB の全体的な関係を示しています。

![アカウント、コンテナー、およびページ BLOB の関係を示すスクリーンショット](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>指定したサイズの空のページ BLOB を作成する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

最初に、コンテナーへの参照を取得します。 ページ BLOB を作成するには、GetPageBlobClient メソッドを呼び出してから、[PageBlobClient.Create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) メソッドを呼び出します。 作成する BLOB の最大サイズを渡します。 サイズは 512 バイトの倍数にする必要があります。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

ページ BLOB を作成するには、まず、次の例で示すように、**StorageCredentialsAccountAndKey** オブジェクトと共に **CloudBlobClient** オブジェクトを作成します (ストレージ アカウント (図 1 の *pbaccount*) の BLOB ストレージにアクセスするためのベース URI を使用します)。 この例では、**CloudBlobContainer** オブジェクトへの参照の作成と、まだ存在していない場合のコンテナー (*testvhds*) の作成を示しています。 次に、**CloudBlobContainer** オブジェクトを使用して、アクセスするページ BLOB の名前 (os4.vhd) を指定することで、**CloudPageBlob** オブジェクトへの参照を作成します。 ページ BLOB を作成するには、[CloudPageBlob.Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create) を呼び出し、作成する BLOB の最大サイズを渡します。 *blobSize* は 512 バイトの倍数にする必要があります。

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>ページ BLOB のサイズを変更する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

ページ BLOB のサイズを作成後に変更するには、[Resize](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize) メソッドを使用します。 要求するサイズは 512 バイトの倍数でなければなりません。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

ページ BLOB のサイズを作成後に変更するには、[Resize](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) メソッドを使用します。 要求するサイズは 512 バイトの倍数でなければなりません。

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>ページ BLOB にページを書き込む

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

ページを書き込むには、[PageBlobClient.UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) メソッドを使用します。  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

ページを書き込むには、[CloudPageBlob.WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) メソッドを使用します。  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

これにより、最大 4MB の連続した一連のページを書き込むことができます。 書き込み先のオフセットは、512 バイト境界で開始し (startingOffset %512 = = 0)、512 境界 - 1 で終了する必要があります。 

連続した一連のページの書き込み要求が BLOB サービスで成功し、耐久性と回復性がレプリケートされるとすぐに、書き込みがコミットされ、クライアントに成功が返されます。  

下の図は、2 つの別個の書き込みの操作を示しています。

![2 つの別個の書き込みの操作を示す図。](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  長さ 1024 バイトのオフセット 0 で開始する書き込み操作 
2.  長さ 1024 のオフセット 4096 で開始する書き込み操作 

#### <a name="reading-pages-from-a-page-blob"></a>ページ BLOB からページを読み取る

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

ページを読み取るには、[PageBlobClient.Download](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download) メソッドを使用して、ページ BLOB から一定範囲のバイトを読み取ります。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

ページを読み取るには、[CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) メソッドを使用して、ページ BLOB から特定の範囲のバイトを読み取ります。 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

これにより、BLOB 全体、または BLOB 内の任意のオフセットから始まるバイトの範囲をダウンロードすることができます。 読み取りの際は、オフセットが 512 の倍数で開始する必要はありません。 NUL ページからバイトを読み取ると、サービスが 0 バイトを返します。

次の図は、オフセットが 256 で範囲のサイズが 4352 の読み取り操作を示しています。 返されるデータはオレンジ色で強調表示されています。 NUL ページの場合はゼロが返されます。

![オフセットが 256 で範囲のサイズが 4352 の読み取り操作を示す図](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

データがほとんど設定されていない BLOB がある場合は、0 バイトの送信に対する支払いを回避したり、ダウンロードの待機時間を短縮したりするために、有効なページ領域だけをダウンロードすることもできます。  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

データに裏付けられているページを確認するには、[PageBlobClient.GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges) を使用します。 その後、返される範囲を列挙し、各範囲内のデータをダウンロードできます。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

データによって提供されるページを確認するには、[CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges) を使用します。 その後、返される範囲を列挙し、各範囲内のデータをダウンロードできます。 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>ページ BLOB をリースする

BLOB のリース操作は、書き込み操作と削除操作のための BLOB のロックを設定および管理します。 この操作は、ページ BLOB が複数のクライアントからアクセスされているというシナリオで、一度に 1 つのクライアントしか BLOB に書き込めないようにする場合に便利です。 たとえば、Azure ディスクは、ディスクが確実に 1 つの VM でのみ管理されるようにするために、このリース メカニズムを利用します。 ロック期間は、15 ～ 60 秒にすることも、無限にすることもできます。 詳細については、[こちら](/rest/api/storageservices/lease-blob)のドキュメントを参照してください。

ページ BLOB は、豊富な REST API だけでなく、共有アクセス、耐久性、および強化されたセキュリティも提供します。 これらのメリットについて、次の段落で詳しく説明します。 

### <a name="concurrent-access"></a>同時アクセス

ページ BLOB の REST API とそのリース メカニズムにより、アプリケーションが複数のクライアントからページ BLOB にアクセスすることができます。 たとえば、ストレージ オブジェクトを複数のユーザーと共有する分散クラウド サービスをビルドする必要があるとします。 これは、複数のユーザーにイメージの大規模なコレクションを提供する Web アプリケーションである可能性があります。 これを実装するための 1 つのオプションは、接続されたディスクと VM を使用することです。 この欠点として、(i) ディスクが 1 つの VM にしか接続できない、スケーラビリティや柔軟性が制限され、リスクが増すという制約があります。 VM または VM で実行されているサービスに問題がある場合は、リースのため、リース期限が切れるか中途解約するまでイメージにはアクセスできず、(ii) IaaS VM の所有に追加のコストがかかります。 

もう 1 つのオプションは、Azure Storage REST API 経由でページ BLOB を直接使用することです。 このオプションは、コストの高い IaaS VM が不要で、複数のクライアントからの直接アクセスに十分な柔軟性があり、ディスクの接続/取り外しの必要をなくすことでクラシック デプロイ モデルを簡略化し、VM 上の問題のリスクを排除します。 さらに、ランダムの読み取り/書き込み操作にディスクと同じレベルのパフォーマンスを提供します

### <a name="durability-and-high-availability"></a>耐久性と高可用性

Standard Storage と Premium Storage はどちらも耐久性のあるストレージであり、ページ BLOB のデータは耐久性および高可用性を確保するために常にレプリケートされます。 Azure Storage の冗長性の詳細については、この[ドキュメント](../common/storage-redundancy.md)を参照してください。 Azure は、IaaS ディスクとページ BLOB のエンタープライズ レベルの耐久性を、業界トップ レベルの[年間故障率](https://en.wikipedia.org/wiki/Annualized_failure_rate) 0% で一貫して提供してきました。

### <a name="seamless-migration-to-azure"></a>Azure へのシームレスな移行

独自のカスタマイズしたバックアップ ソリューションの実装に関心をお持ちのお客様と開発者には、Azure は、デルタを保持するだけの増分スナップショットも提供しています。 この機能により最初の完全コピーのコストがかからないため、バックアップのコストを大幅に削減できます。 差分データを効率的に読み取りおよびコピーする機能だけでなく、これは開発者からのさらなるイノベーションを可能にするもう 1 つの強力な機能であり、Azure でのクラス最高のバックアップとディザスター リカバリー (DR) のエクスペリエンスにつながります。 [Get Page Ranges](/rest/api/storageservices/get-page-ranges) API と、DR の増分データを簡単にコピーするために使用できる [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob) API と共に、[Blob スナップショット](/rest/api/storageservices/snapshot-blob)を使用して、Azure の VM 用の独自のバックアップまたは DR ソリューションをセットアップできます。 

さらに、多くの企業では、重要なワークロードがオンプレミスのデータ センターで既に実行されています。 ワークロードをクラウドに移行する場合の主な関心事の 1 つは、データをコピーするために必要なダウンタイムの時間と、切り替え後の予期しない問題のリスクです。 多くの場合、ダウンタイムは、クラウドへの移行で致命的な問題になる可能性があります。 Azure は、ページ BLOB の REST API を使用して、重要なワークロードの中断を最小限にしてクラウドの移行を実現することで、この問題を解決します。 

スナップショットの作成方法とスナップショットからのページ BLOB の復元方法の例については、[増分スナップショットを使用したバックアップ プロセスの設定](../../virtual-machines/windows/incremental-snapshots.md)に関する記事を参照してください。