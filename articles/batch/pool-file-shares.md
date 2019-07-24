---
title: Azure Batch プールの Azure ファイル共有 | Microsoft Docs
description: Azure Batch の Linux または Windows プールの計算ノードから Azure Files 共有をマウントする方法
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 1e9d039769e7fbcb9c2b7285aa727acd7322bcdf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103334"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Batch プールと共に Azure ファイル共有を使用する

Azure Files はクラウドで、[Server Message Block (SMB) プロトコル](../storage/files/storage-files-introduction.md)を介してアクセスできる、完全に管理されたファイル共有を提供します。 この記事では、プールの計算ノード上に Azure ファイル共有をマウントして使用するための情報とコード サンプルを示します。 コード サンプルでは、Batch .NET と Python SDK を使用していますが、他の Batch SDK やツールを使用しても同様の操作を実行できます。

Batch では、Azure Storage の Blob を使用してデータの読み取りと書き込みを行うために、ネイティブ API のサポートを提供しています。 ただし、場合によっては、お使いのプールの計算ノードから Azure ファイル共有にアクセスする必要が生じます。 たとえば、SMB ファイル共有に依存している従来のワークロードがある場合や、お使いのタスクで共有データにアクセスしたり共有の出力を生成したりする必要がある場合です。 

## <a name="considerations-for-use-with-batch"></a>Batch と共に使用するための考慮事項

* 比較的少数の並行タスクを実行するプールがある場合、Azure ファイル共有の使用を検討します。 [パフォーマンスとスケールの対象](../storage/files/storage-files-scale-targets.md)を確認し、必要なプール サイズとアセット ファイル数を仮定して、(Azure Storage アカウントを利用する) Azure Files を使用する必要があるかを判断します。 

* Azure ファイル共有は[コスト効率が高く](https://azure.microsoft.com/pricing/details/storage/files/)、他のリージョンへのデータ レプリケーションを使って構成できるため、グローバルな冗長性を備えます。 

* オンプレミス コンピューターから Azure ファイル共有を同時にマウントできます。

* Azure ファイル共有の一般的な[計画に関する考慮事項](../storage/files/storage-files-planning.md)についても確認してください。


## <a name="create-a-file-share"></a>ファイル共有を作成する

お使いの Batch アカウントに関連付けられたストレージ アカウント、または別個のストレージ アカウントに、[ファイル共有を作成](../storage/files/storage-how-to-create-file-share.md)します。

## <a name="mount-a-share-on-a-windows-pool"></a>Windows プール上に共有をマウントする

このセクションでは、Azure ファイル共有を Windows ノードのプール上にマウントして使用する手順とコード サンプルを示します。 詳しい背景については、Windows への Azure ファイルのマウントに関する[資料](../storage/files/storage-how-to-use-files-windows.md)を参照してください。 

Batch では、タスクが Windows ノード上で実行されるたびに共有をマウントする必要があります。 現時点では、Windows ノード上にタスク間のネットワーク接続を保持することはできません。

たとえば、各タスクのコマンド ラインの一部としてファイル共有をマウントするための `net use` コマンドを含めます。 ファイル共有をマウントするには、次の資格情報が必要になります。

* **[ユーザー名]**: AZURE\\\<storageaccountname\> (たとえば、AZURE\\*mystorageaccountname*)
* **パスワード**: <StorageAccountKeyWhichEnds in==> (たとえば、*XXXXXXXXXXXXXXXXXXXXX==*)

次のコマンドは、ストレージ アカウント *mystorageaccountname* にあるファイル共有 *myfileshare* を *S:* ドライブとしてマウントします。

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

わかりやすくするため、この例では、テキストで直接、資格情報を渡しています。 実際には、環境変数、証明書、または Azure Key Vault などのソリューションを使用して資格情報を管理することを強くお勧めします。

マウント操作を簡単にするために、必要に応じてノード上に資格情報を保持します。 その後は、資格情報なしで共有をマウントすることができます。 次の 2 つの手順を実行します。

1. プールの構成で開始タスクを使用して、`cmdkey` コマンドライン ユーティリティを実行します。 これにより、各 Windows ノードで資格情報が保持されます。 開始タスクのコマンド ラインは、次のようになります。

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. `net use` を使用して各タスクの一部として、各ノード上に共有をマウントします。 たとえば、次のタスク コマンド ラインは、*S:* ドライブとしてファイル共有をマウントします。 この後には、共有を参照するコマンドまたはスクリプトが続きます。 キャッシュされた資格情報は、`net use` の呼び出しの中で使用されます。 この手順では、プール上の開始タスクで使用したタスクと同じユーザー ID を使用していると仮定しています。すべてのシナリオに当てはまるわけではありません。

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C# の例
次の C# の例は、開始タスクを使用して Windows プール上に資格情報を保持する方法を示しています。 ストレージ ファイル サービス名とストレージの資格情報が、定義済みの定数として渡されます。 ここでは、プールのスコープを使って、標準の (管理者以外の) 自動ユーザー アカウントで開始タスクが実行されます。

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

資格情報を格納した後、タスク コマンド ラインを使用して共有をマウントし、読み取りまたは書き込み操作で共有を参照します。 基本の例として、次のコード スニペットのタスク コマンド ラインでは、ファイル共有のファイルを一覧表示するために `dir` コマンドを使用しています。 必ず、プールで開始タスクの実行に使用した ID と同じ[ユーザー ID](batch-user-accounts.md) を使用して、各ジョブ タスクを実行してください。 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Linux プール上で共有をマウントする

Azure ファイル共有は、[CIFS カーネル クライアント](https://wiki.samba.org/index.php/LinuxCIFS)を使用して Linux ディストリビューションにマウントできます。 次の例は、Ubuntu 16.04 LTS 計算ノードのプール上でファイル共有をマウントする方法を示しています。 異なる Linux ディストリビューションを使用する場合、一般的な手順はほぼ同じですが、ディストリビューションに適したパッケージ マネージャーを使用してください。 詳細およびその他の例については、「[Linux で Azure Files を使用する](../storage/files/storage-how-to-use-files-linux.md)」を参照してください。

最初に、管理者ユーザー ID で、`cifs-utils` パッケージをインストールして、ローカル ファイルシステムにマウント ポイント (たとえば、*/mnt/MyAzureFileShare*) を作成します。 マウント ポイント用のフォルダーはファイル システム上のどこにでも作成できますが、`/mnt` フォルダー下にこれを作成するのが一般的な規則です。 必ず、`/mnt` (Ubuntu 上) または `/mnt/resource` (他のディストリビューション上) に直接、マウント ポイントを作成しないようにしてください。

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

次に、以下の資格情報を指定して、`mount` コマンドを実行してファイル共有をマウントします。

* **ユーザー名**: \<storageaccountname\> (たとえば、*mystorageaccountname*)
* **パスワード**: <StorageAccountKeyWhichEnds in==> (たとえば、*XXXXXXXXXXXXXXXXXXXXX==*)

次のコマンドは、ストレージ アカウント *mystorageaccountname* にあるファイル共有 *myfileshare* を */mnt/MyAzureFileShare* でマウントします。 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

わかりやすくするため、この例では、テキストで直接、資格情報を渡しています。 実際には、環境変数、証明書、または Azure Key Vault などのソリューションを使用して資格情報を管理することを強くお勧めします。

Linux プール上では、これらのすべての手順を単一の開始タスク内にまとめたり、1 つのスクリプト内で実行したりできます。 管理者ユーザーとして、プール上で開始タスクを実行します。 正常に完了するまで待機してから、共有を参照するプール上でのタスクをさらに実行するように、開始タスクを設定します。

### <a name="python-example"></a>Python の例

次の Python の例では、Ubuntu プールを構成して開始タスクで共有をマウントする方法を示します。 マウント ポイント、ファイル共有エンドポイント、およびストレージの資格情報が、定義済みの定数として渡されます。 プールのスコープを使って、管理者の自動ユーザー アカウントで開始タスクが実行されます。

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

共有をマウントしてジョブを定義した後、タスク コマンド ラインでその共有を使用します。 たとえば、次の基本のコマンドは `ls` を使用して、ファイル共有内のファイルを一覧表示します。

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>次の手順

* Batch でデータの読み取りと書き込みを行うためのその他のオプションについては、[Batch 機能の概要](batch-api-basics.md) に関するページと「[ジョブとタスク出力を保持する](batch-task-output.md)」を参照してください。

* また、Batch コンテナー ワークロードにファイル システムをデプロイするための[Shipyard レシピ](https://github.com/Azure/batch-shipyard/tree/master/recipes) を含む [Batch Shipyard](https://github.com/Azure/batch-shipyard) ツールキットも参照してください。