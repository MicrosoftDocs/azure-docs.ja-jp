---
title: レンダリング用のストレージとデータ移動
description: アセット ファイルと出力ファイルのワークロードをレンダリングするためのさまざまなストレージとデータ移動のオプションについて説明します。
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 55ec04df2a107dabfc72298bc8849c13f3a926e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86147311"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>アセット ファイルと出力ファイルをレンダリングするためのストレージとデータ移動のオプション

次のように、プール VM 上のレンダリング アプリケーションでシーンやアセットのファイルを有効にするオプションは複数あります。

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md):
  * シーンおよびアセットのファイルは、ローカル ファイル システムから BLOB ストレージにアップロードされます。 アプリケーションがタスクによって実行されると、レンダリング アプリケーションがアクセスできるように、必要なファイルが BLOB ストレージから VM にコピーされます。 出力ファイルはレンダリング アプリケーションから VM ディスクに書き込まれ、その後 BLOB ストレージにコピーされます。  必要に応じて、出力ファイルを BLOB ストレージからローカル ファイル システムにダウンロードできます。
  * Azure Blob Storage は、小規模なプロジェクト向けの簡単かつコスト効率に優れたオプションです。  すべてのアセット ファイルは各プール VM で必須であるため、アセット ファイルの数とサイズが増えた場合は、ファイル転送ができるだけ効率的になるように、適切に管理する必要があります。  
* [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) を使用したファイル システムとしての Azure ストレージ:
  * Linux の VM の場合、blobfuse 仮想ファイル システム ドライバーが使用されると、ストレージ アカウントを公開してファイル システムとして使用できます。
  * このオプションではファイル システムに VM が必要とされないため、非常にコスト効率がよいという利点があります。さらに、VM での blobfuse のキャッシングにより、複数のジョブやタスクで同じファイルが繰り返しダウンロードされることを防ぎます。  また、ファイルは単純な BLOB および標準の API やツールであるため、データ移動も簡単です。たとえば、azcopy を使用してオンプレミスのファイル システムと Azure ストレージ間でファイルをコピーできます。
* ファイル システムまたはファイル共有:
  * VM のオペレーティング システムやパフォーマンス/スケールの要件により、[Azure Files](../storage/files/storage-files-introduction.md)、NFS に接続されたディスクと VM の使用、GlusterFS などの分散ファイル システムに接続されたディスクと複数の VM の使用、またはサード パーティのオファリングの使用のオプションがあります。
  * マイクロソフトは [Avere Systems](https://www.averesystems.com/) を買収し、近い将来に大規模で高パフォーマンスのレンダリングに最適なソリューションが完成します。  Avere のソリューションでは、BLOB ストレージやオンプレミスの NAS デバイスと連携する、Azure ベースの NFS や SMB キャッシュを作成できます。
  * ファイル システムを使用すると、ファイルはファイル システムに対して直接読み取りと書き込みが可能で、ファイル システムとプール VM 間でコピーできます。
  * 共有ファイル システムはプロジェクトおよびジョブ間で大量のアセットを共有可能で、レンダリング タスクは必要なものにのみアクセスします。

## <a name="using-azure-blob-storage"></a>Azure Blob Storage を使用する

BLOB ストレージ アカウントまたは汎用 v2 ストレージ アカウントを使用する必要があります。  [このブログ記事](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)で説明されているように、これら 2 つの種類のストレージ アカウントは、汎用 v1 ストレージ アカウントと比較して非常に高い制限で構成できます。  構成すると、より高い制限により、特にストレージ アカウントにアクセスするプール VM の数が多いときに、より優れたパフォーマンスとスケーラビリティを実現します。

### <a name="copying-files-between-client-and-blob-storage"></a>クライアントと BLOB ストレージ間でファイルをコピーする

Azure ストレージとの間のファイルのコピーには、ストレージ BLOB API、[Azure Storage Data Movement Library](https://github.com/Azure/azure-storage-net-data-movement)、[Windows](../storage/common/storage-use-azcopy-v10.md) または [Linux](../storage/common/storage-use-azcopy-v10.md) の azcopy コマンド ライン ツール、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)、[Azure Batch Explorer](https://azure.github.io/BatchExplorer/) など、さまざまなメカニズムを使用できます。

たとえば、azcopy を使用して、次のようにフォルダー内のすべてのアセットを転送できます。


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

変更されたファイルのみをコピーするには、次のように /XO パラメーターを使用できます。

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>入力アセット ファイルを BLOB ストレージから Batch のプール VM にコピーする

ジョブ アセットのサイズに応じて、ファイルをコピーする方法は複数あります。
最も簡単な方法は、次のように各ジョブに対してプール VM にすべてのアセット ファイルをコピーする方法です。

* ジョブに一意のファイルがあり、ジョブのすべてのタスクで必要とされるとき、すべてのファイルをコピーするように[ジョブ準備タスク](/rest/api/batchservice/job/add#jobpreparationtask)を指定できます。  ジョブ準備タスクは、最初のジョブ タスクが VM で実行されるときに 1 回実行され、後続のジョブ タスクでは実行されません。
* ジョブが完了したときにジョブごとにファイルを削除するには、[ジョブ解放タスク](/rest/api/batchservice/job/add#jobreleasetask)を指定してください。これにより、VM のディスクがジョブのすべてのアセット ファイルでいっぱいになることを回避できます。
* 同じアセットを使用する複数のジョブがあり、各ジョブでアセットに対する変更が増分変更のみであるとき、サブセットのみが更新された場合でも、すべてのアセット ファイルがコピーされます。  これは、大規模なアセット ファイルが大量にあるときに非効率になります。

アセット ファイルがジョブの間でのみ再利用され、ジョブ間での変更が増分変更のみのとき、より効率的でわずかに入り組んだ方法は、VM 上の共有フォルダーにアセットを格納し、変更されたファイルを同期する方法です。

* ジョブ準備タスクでは、azcopy を /XO パラメーターと共に使用して、AZ_BATCH_NODE_SHARED_DIR 環境変数で指定された VM の共有フォルダーに対してコピーを実行します。  これにより、各 VM に変更されたファイルのみがコピーされます。
* プール VM の一時ドライブに収まるように、すべてのアセットのサイズを考慮する必要があります。

Azure Batch には、ストレージ アカウントと Batch のプール VM 間でファイルをコピーする組み込みのサポートが用意されています。  タスク リソース ファイルはストレージからプール VM にファイルをコピーし、[ジョブ準備タスク](/rest/api/batchservice/job/add#resourcefile)で指定できます。  残念ながら、何百ものファイルがあるとき、上限に達してタスクが失敗する可能性があります。  大量のアセットがあるときは、ジョブ準備タスクにワイルドカードを使用できるかつ上限のない azcopy コマンドラインを使用することをお勧めします。

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>出力ファイルを Batch のプール VM から BLOB ストレージにコピーする

[出力ファイル](/rest/api/batchservice/task/add#outputfile)はプール VM からストレージにファイルをコピーするために使用できます。  タスクが完了したら、VM から指定されたストレージ アカウントに 1 つ以上のファイルをコピーできます。  表示される出力をコピーする必要がありますが、ログ ファイルを格納することが望ましい場合もあります。

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Linux の VM プールに blobfuse 仮想ファイル システムを使用する

blobfuse は、ストレージ アカウントに BLOB として格納されているファイルに Linux ファイル システム経由でアクセスできるようにする、Azure Blob Storage 用の仮想ファイル システム ドライバーです。

プールのノードは開始時にファイル システムをマウントする場合や、ジョブ準備タスクの一環として発生する場合があります。ジョブ準備タスクは、ジョブの最初のタスクがノード上で実行されるときにのみ実行されるタスクです。  blobfuse は、ramdisk と VM のローカル SSD の両方を活用してファイルをキャッシングするように構成できます。これにより、ノード上の複数のタスクがいくつかの同じファイルにアクセスする場合のパフォーマンスが大幅に改善します。

blobfuse ファイル システムを使用してスタンドアロンの V-Ray レンダーを実行し、他のアプリケーション用のテンプレートのベースとして使用できる、[サンプル テンプレートが用意されています](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount)。

### <a name="accessing-files"></a>ファイルにアクセスする

ジョブ タスクは、マウントされたファイル システムを使用して、入力ファイルと出力ファイルのパスを指定します。

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>入力アセット ファイルを BLOB ストレージから Batch のプール VM にコピーする

ファイルは単純に Azure Storage 内の BLOB であるため、標準の BLOB API、ツール、および UI をオンプレミスのファイル システムと BLOB ストレージ間でのファイルのコピーに使用できます。たとえば、azcopy、Storage Explorer、Batch Explorer などです。

## <a name="using-azure-files-with-windows-vms"></a>Windows の VM で Azure Files を使用する

[Azure Files](../storage/files/storage-files-introduction.md) はクラウドで、SMB プロトコルを介してアクセスできる、フル マネージドのファイル共有を提供します。  Azure Files は AAzure Blob Storage をベースとし、[コスト効率が高く](https://azure.microsoft.com/pricing/details/storage/files/)、他のリージョンへのデータ レプリケーションを使用して構成できるため、グローバルな冗長性を備えます。  プール サイズとアセット ファイルの数の予測から、Azure Files を使用する必要があるかどうかを判断するには、[スケール ターゲット](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets)を確認する必要があります。

Azure ファイル共有をマウントする方法について説明する[ドキュメント](../storage/files/storage-how-to-use-files-windows.md)が用意されています。

### <a name="mounting-an-azure-files-share"></a>Azure ファイル共有をマウントする

Batch で使用するには、タスク間の接続を保持することはできないため、タスクが実行されるたびにマウント操作を実行する必要があります。  これを行う最も簡単な方法は、プールの構成で cmdkey で開始タスクを使用して資格情報を保持し、各タスクの前に共有をマウントする方法です。

次にプール テンプレートで cmdkey を使用する例を示します (JSON ファイルでの使用のためにエスケープされています)。net use の呼び出しから cmdkye の呼び出しを分離するときは、開始タスクのユーザー コンテキストをタスクの実行に使用されるものと同じにする必要があります。

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

ジョブ タスクのコマンドラインの例:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>ファイルにアクセスする

ジョブ タスクは、マッピングされたドライブまたは UNC パスのいずれかを使用し、マウントされたファイル システムを使用して、入力ファイルと出力ファイルのパスを指定します。

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>入力アセット ファイルを BLOB ストレージから Batch のプール VM にコピーする

Azure Files は、azcopy、Azure CLI、Storage Explorer、Azure PowerShell、Batch Explorer など、Azure Storage をサポートするすべての主要な API やツールでサポートされています。

[Azure File Sync](../storage/files/storage-sync-files-planning.md) は、オンプレミスのファイル システムと Azure ファイル共有間でファイルを自動的に同期できます。

## <a name="next-steps"></a>次のステップ

ストレージのオプションについて詳しくは、次のドキュメントで掘り下げています。

* [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Azure Files](../storage/files/storage-files-introduction.md)
