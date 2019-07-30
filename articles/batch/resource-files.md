---
title: リソース ファイルを作成して使用する - Azure Batch | Microsoft Docs
description: さまざまな入力ソースから Azure Batch リソース ファイルを作成する方法について説明します。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 9c55b22d1cb85fb645087cf48b54f9d5ac12d58f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322181"
---
# <a name="creating-and-using-resource-files"></a>リソース ファイルを作成して使用する

Azure Batch タスクには多くの場合、何らかの形の処理するデータが必要です。 リソース ファイルは、このデータをタスク経由でバッチ仮想マシン (VM) に提供する手段です。 タスク、開始タスク、ジョブ準備タスク、ジョブ解放タスクなど、すべてのタイプのタスクがリソース ファイルをサポートします。この記事は、リソース ファイルを作成し、それらを VM 上に配置するいくつかの一般的な方法について説明します。  

リソース ファイルはバッチ内の VM にデータを格納するためのメカニズムですが、データの型とその使用方法は柔軟に選択できます。 ただし、いくつかの一般的なユース ケースがあります。

1. 共通ファイルを開始タスクでリソース ファイルを使用して各 VM 上にプロビジョニングします。
1. タスクによって処理される入力データをプロビジョニングします。

共通ファイルとは、たとえば、タスクが実行するアプリケーションをインストールするために使用される開始タスクのファイルです。 入力データは、バッチにより処理される raw 画像やビデオ データ、その他の任意の情報が可能です。

## <a name="types-of-resource-files"></a>リソース ファイルの種類

リソース ファイルの生成に使用できるいくつかの異なるオプションがあります。 リソース ファイルの作成プロセスは、元のデータの格納場所によって異なります。

リソース ファイルを作成するためのオプション:

- [ストレージ コンテナーの URL](#storage-container-url):Azure で任意のストレージ コンテナーからリソース ファイルを生成します
- [ストレージ コンテナー名](#storage-container-name):バッチにリンクされている Azure ストレージ アカウントで、コンテナーの名前からリソース ファイルを生成します
- [Web エンドポイント](#web-endpoint):有効な任意の HTTP URL からリソース ファイルを生成します

### <a name="storage-container-url"></a>ストレージ コンテナーの URL

ストレージ コンテナーの URL を使用するということは、適切なアクセス許可があれば、Azure にあるどのストレージ コンテナー内のファイルにもアクセスできることを意味します。

この C# の例では、ファイルは、Blob ストレージとして Azure ストレージ コンテナーに既にアップロードされています。 リソース ファイルを作成するために必要なデータにアクセスするには、まずストレージ コンテナーにアクセスする必要があります。

ストレージ コンテナーにアクセスするための正しいアクセス許可を使用して、共有アクセス署名 (SAS) URI を作成します。 SAS に有効期限の時間とアクセス許可を設定します。 この場合、開始時刻が指定されていないため、SAS がすぐに有効になり、生成後 2 時間で有効期限が切れます。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> コンテナーへのアクセスでは、`Read` と `List` の両方のアクセス許可が必要ですが、BLOB へのアクセスでは、必要なのは `Read` アクセス権限のみです。

アクセス許可を構成したら、SAS トークンを作成し、ストレージ コンテナーにアクセスするための SAS URL を書式設定します。 ストレージ コンテナーの書式設定された SAS URL を使用して、[`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet) でリソース ファイルを生成します。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

SAS URL を生成する代わりに、Azure Blob Storage のコンテナーとその BLOB に対する匿名のパブリック読み取りアクセスを有効にすることもできます。 そうすることで、アカウント キーを共有せず、SAS も必要とせずに、これらのリソースに対する読み取り専用のアクセスを付与できます。 パブリック読み取りアクセスは、特定の BLOB を匿名読み取りアクセスで常に使用できるようにするシナリオに一般的に使用されます。 このシナリオがソリューションに適している場合は、[BLOB への匿名アクセス](../storage/blobs/storage-manage-access-to-resources.md)の記事を参照し、BLOB データへのアクセスの管理の詳細について学習してください。

### <a name="storage-container-name"></a>ストレージ コンテナー名

SAS URL を構成して作成する代わりに、BLOB データにアクセスするために Azure ストレージ コンテナーの名前を使用できます。 使用されるストレージ コンテナーは、autostorage アカウントと呼ばれる Batch アカウントにリンクされている Azure ストレージ アカウントにある必要があります。 autostorage アカウントのストレージ コンテナー名を使用すると、ストレージ コンテナーにアクセスするための SAS URL の構成と作成をバイパスすることができます。

この例では、リソース ファイルの作成に使用するデータは、Batch アカウントにリンクされている Azure ストレージ アカウントに既にあると仮定しています。 Autostorage アカウントを持っていない場合、アカウントを作成してリンクする方法の詳細については、[Batch アカウントの作成](batch-account-create-portal.md)の手順を参照してください。

リンクされたストレージ アカウントを使用することで、ストレージ コンテナーへの SAS URL の作成と構成は必要なくなります。 代わりに、リンクされたストレージ アカウントでストレージ コンテナーの名前を指定します。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web エンドポイント

Azure Storage にアップロードされていないデータも、リソース ファイルの作成に使用できます。 入力データを含む任意の有効な HTTP URL を指定できます。 URL は Batch API に提供され、データはリソース ファイルを作成するために使用されます。

次の C# 例では、入力データは架空の GitHub エンドポイントでホストされています。 API は、有効な Web エンドポイントからファイルを取得し、タスクによって消費されるリソース ファイルを生成します。 このシナリオには資格情報は必要ありません。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>ヒントと検索候補

各 Azure Batch タスクはファイルをさまざまな方法で使用します。そのため、Batch にはタスク上のファイルを管理するために使用できるオプションがあります。 次のシナリオは、包括的なものとなるようには意図していません。代わりに、いくつかの一般的な状況を扱い、推奨事項を提供することを意図しています。

### <a name="many-resource-files"></a>多くのリソース ファイル

Batch ジョブには、すべてが同じ共通ファイルを使用する複数のタスクを含めることができます。 共通タスク ファイルが多くのタスク間で共有される場合、リソース ファイルを使用する代わりに、ファイルを含むアプリケーション パッケージを使用するほうが良い選択肢となる場合があります。 アプリケーション パッケージは、ダウンロード速度の最適化を提供します。 さらに、アプリケーション パッケージ内のデータはタスク間でキャッシュされるため、タスク ファイルがそれほど変更されない場合には、アプリケーション パッケージがソリューションとして適しています。 アプリケーション パッケージを使用すると、複数のリソース ファイルを手動で管理したり、Azure Storage 内のファイルにアクセスするために SAS URL を生成したりする必要はありません。 Batch は、バックグラウンドで Azure Storage と連携して、アプリケーション パッケージを格納したりコンピューティング ノードにデプロイしたりします。

各タスクにそのタスクに固有の多数のファイルがある場合、リソース ファイルはおそらく最適なオプションです。 一意のファイルを使用するタスクは多くの場合、更新または交換する必要があるため、アプリケーション パッケージのコンテンツを使用して実行するほど簡単ではありません。 リソース ファイルには、個々のファイルの更新、追加、または編集のためのさらに高い柔軟性があります。

### <a name="number-of-resource-files-per-task"></a>タスクあたりのリソース ファイルの数

数百のリソース ファイルがタスクに指定されている場合、Batch はタスクが大きすぎるためにそれを拒否する可能性があります。 タスク自体のリソース ファイルの数を最小限に抑えることで、タスクを小さくしておくことをお勧めします。

タスクが必要とするファイル数を最小限に抑える方法がない場合、リソース ファイルのストレージ コンテナーを参照する 1 つのリソース ファイルを作成することで、タスクを最適化できます。 これを実行するには、Azure ストレージ コンテナーにリソース ファイルを配置して、リソース ファイルのさまざまな「コンテナー」モードを使用します。 BLOB プレフィックス オプションを使用して、タスク用にダウンロードされるファイルのコレクションを指定します。

## <a name="next-steps"></a>次の手順

- リソース ファイルの代替としての[アプリケーション パッケージ](batch-application-packages.md)について説明します。
- リソース ファイルにコンテナーを使用することの詳細については、「[Container workloads](batch-docker-container-workloads.md)」(コンテナー ワークロード) を参照してください。
- タスクからの出力データを収集して保存する方法については、「[ジョブとタスクの出力の保持](batch-task-output.md)」(Persist job and task output) を参照してください。
- Batch ソリューションの構築に使用できる [Batch API とツール](batch-apis-tools.md)について学習します。
