---
title: ユーザー アカウントでタスクを実行する - Azure Batch | Microsoft Docs
description: Azure Batch でタスクを実行するためのユーザー アカウントを構成する
services: batch
author: laurenhughes
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 93b3004303dd1587340b467c2fd52cb7233c95fd
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322710"
---
> [!NOTE] 
> この記事で説明するユーザー アカウントは、セキュリティ上の理由により、Remote Desktop Protocol (RDP) や Secure Shell (SSH) で使用されているユーザー アカウントとは異なります。 
>
> SSH を使用して Linux 仮想マシンの構成を実行しているノードに接続するには「[リモート デスクトップを使用した Azure の Linux VM への接続](../virtual-machines/virtual-machines-linux-use-remote-desktop.md)に関するページをご覧ください。 RDP を使用して Windows を実行しているノードに接続するには、[Windows Server VM への接続](../virtual-machines/windows/connect-logon.md)に関するページをご覧ください。<br /><br />
> RDP を使用してクラウド サービスの構成を実行しているノードに接続するには、「[Azure Cloud Services のロールでのリモート デスクトップ接続の有効化](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md)」をご覧ください。
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Batch のユーザー アカウントでタスクを実行する

Azure Batch のタスクは、常にユーザー アカウントのもとで実行されます。 既定では、管理者権限のない標準ユーザー アカウントでタスクが実行されます。 通常は、既定のユーザー アカウントの設定で十分です。 ただし、タスクを実行するために、特別にユーザー アカウントを構成したほうが良い場合もあります。 この記事では、ユーザー アカウントの種類、および自分の用途に合わせてユーザー アカウントを構成する方法について説明します。

## <a name="types-of-user-accounts"></a>ユーザー アカウントの種類

Azure Batch には、タスクを実行するためのユーザー アカウントが 2 種類用意されています。

- **自動ユーザー アカウント。** 自動ユーザー アカウントは、Batch サービスによって自動的に作成される、組み込みのユーザー アカウントです。 既定では、タスクは自動ユーザー アカウントで実行されます。 あるタスク向けに自動ユーザーの仕様を構成することで、どの自動ユーザー アカウントでタスクを実行するかを指定できます。 タスクを実行する自動ユーザー アカウントの昇格のレベルとスコープを、自動ユーザーの仕様で指定できます。 

- **名前付きユーザー アカウント。** プールを作成するときに、プールに対して 1 つ以上の名前付きユーザー アカウントを指定できます。 各ユーザー アカウントは、プールの各ノード上に作成されます。 アカウント名の他に、ユーザー アカウントのパスワード、昇格レベルを指定し、Linux プールの場合は SSH 秘密キーも指定します。 タスクを追加する場合は、そのタスクを実行する名前付きユーザー アカウントを指定できます。

> [!IMPORTANT] 
> Batch サービス バージョン 2017-01-01.4.0 では互換性を損ねる変更が行われ、このバージョンを呼び出すにはコードを更新する必要があります。 Batch の古いバージョンからコードを移行する場合、**runElevated** プロパティは REST API または Batch クライアント ライブラリではサポートされなくなることに注意してください。 昇格レベルの指定には、タスクの新しい **userIdentity** プロパティを使用してください。 クライアント ライブラリのいずれかをご使用の場合は、「[コードを最新の Batch クライアント ライブラリに更新する](#update-your-code-to-the-latest-batch-client-library)」のセクションから、Batch コードの更新に関する簡単なガイドラインをご覧ください。
>
>

## <a name="user-account-access-to-files-and-directories"></a>ファイルとディレクトリへのユーザー アカウント アクセス

自動ユーザー アカウントと名前付きユーザー アカウントの両方に、タスクの作業ディレクトリ、共有ディレクトリ、およびマルチインスタンス タスク ディレクトリへの読み取り/書き込みアクセスがあります。 両方の種類のアカウントには、起動とジョブ準備のためのディレクトリに対する読み取りアクセスがあります。

開始タスクの実行に使用されたものと同じアカウントでタスクが実行される場合、このタスクには開始タスク ディレクトリへの読み取り/書き込みアクセスがあります。 同様に、ジョブの準備タスクの実行に使用されたものと同じアカウントでタスクが実行される場合、このタスクにはジョブの準備タスク ディレクトリへの読み取り/書き込みアクセスがあります。 タスクが開始タスクやジョブ準備タスクとは異なるアカウントで実行されている場合、このタスクには、それぞれのディレクトリに対してのみの読み取りアクセスがあります。

タスクからのファイルとディレクトリへのアクセスに関する詳細については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md#files-and-directories)」をご覧ください。

## <a name="elevated-access-for-tasks"></a>タスクの特権アクセス権 

ユーザー アカウントの昇格レベルは、タスクが特権アクセスで実行されるかどうかを示します。 自動ユーザー アカウントおよび名前付きのユーザー アカウントの両方とも、特権アクセスでの実行が可能です。 昇格レベルには次の 2 つのオプションがあります。

- **NonAdmin:** タスクは特権アクセスのない標準ユーザーとして実行されます。 Batch ユーザー アカウントの既定の昇格レベルは常に **NonAdmin** です。
- **Admin:** タスクは特権アクセスを持つユーザーとして実行され、完全な管理者権限が付与されます。 

## <a name="auto-user-accounts"></a>自動ユーザー アカウント

既定では、タスクは自動ユーザー アカウントのもとで、Batch で実行されます。このユーザーは特権アクセスのない標準ユーザーで、タスク スコープがあります。 自動ユーザーの仕様がタスク スコープで構成されている場合、Batch サービスはそのタスク専用の自動ユーザー アカウントを作成します。

タスク スコープのほかに、プール スコープというものがあります。 タスクの自動ユーザーの仕様がプール スコープで構成されている場合は、プール内のどのタスクでも利用可能な自動ユーザー アカウントでタスクが実行されます。 プール スコープの詳細については、プール スコープのある自動ユーザーとしてのタスクの実行に関するセクションを参照してください。   

既定のスコープは、Windows ノードと Linux ノードとで異なります。

- Windows ノードでは、タスクは既定でタスク スコープのもとで実行されます。
- Linux ノードは常にプール スコープのもとで実行されます。

自動ユーザーの仕様は 4 通りの構成が可能で、それぞれ一意の自動ユーザー アカウントに対応します。

- タスク スコープのある管理者以外のアクセス (既定の自動ユーザーの仕様)
- タスク スコープのある管理者 (特権) アクセス
- プール スコープのある管理者以外のアクセス
- プール スコープのある管理者アクセス

> [!IMPORTANT] 
> タスク スコープで実行されるタスクは、ノード上の他のタスクには事実上アクセスできません。 ただし、アカウントへのアクセスを持つ悪意のあるユーザーは、管理者特権で実行されるタスクを送信することによってこの制限を回避し、その他のタスク ディレクトリにアクセスすることが可能です。 悪意のあるユーザーは、RDP または SSH を使用してノードに接続することもできます。 Batch アカウント キーへのアクセスを保護してこのようなシナリオを阻止する必要があります。 自分のアカウントが侵害された疑いがある場合は、キーを再生成してください。
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>特権アクセスを持つ自動ユーザーとしてタスクを実行する

特権アクセスでタスクを実行する必要がある場合、自動ユーザーの仕様を管理者特権用に構成できます。 たとえば、開始タスクには、ノードでソフトウェアをインストールするために特権アクセスが必要な場合があります。

> [!NOTE] 
> 一般的に、特権アクセスは必要な場合にのみ使用することをお勧めします。 ベスト プラクティスとして、目的の成果を得るために最低限必要な特権を付与することをお勧めしています。 たとえば、開始タスクですべてのユーザーではなく現在のユーザー向けにソフトウェアをインストールすると、タスクへの特権アクセスの付与を回避することができます。 開始タスクを含む、同じアカウントで実行する必要があるすべてのタスクに対して、プール スコープの自動ユーザーの仕様と管理者以外のアクセス権を構成できます。 
>
>

次のコード スニペットでは、自動ユーザーの仕様を構成する方法を示します。 この例では、昇格レベルを `Admin` に、スコープを `Task` に設定します。 タスク スコープは既定の設定ですが、例を示すためにここに含めています。

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>プール スコープのある自動ユーザーとしてタスクを実行する

ノードをプロビジョニングするときは、プール内の各ノードで、プール全体の自動ユーザー アカウントが合計で 2 つ (特権アクセスを持つアカウントと特権アクセスのないアカウント) 作成されます。 自動ユーザーのスコープを特定のタスクのプール スコープに設定すると、このタスクがこれら 2 つのプール全体の自動ユーザー アカウントのいずれかで実行されます。 

自動ユーザーのプール スコープを指定すると、管理者アクセス権で実行されるすべてのタスクが、同じプール全体の自動ユーザー アカウントで実行されます。 同様に、管理者アクセスなしで実行されるタスクも、単一のプール全体の自動ユーザー アカウントで実行されます。 

> [!NOTE] 
> これら 2 つのプール全体の自動ユーザー アカウントは、個別のアカウントです。 プール全体の管理者アカウントで実行されるタスクは、標準アカウントで実行されるタスクとデータを共有することができず、その逆も同様です。 
>
>

同じ自動ユーザー アカウントで実行することの利点は、同じノードで実行されているタスク同士がデータを共有できることです。

タスク間のシークレットの共有が有用なシナリオとして、2 つのプール全体の自動ユーザー アカウントのいずれかでタスクを実行している場合があります。 たとえば、開始タスクは、その他のタスクが使用できるノードにシークレットをプロビジョニングする必要があるとします。 Windows データ保護 API (DPAPI) を使用できますが、管理者特権が必要です。 代わりに、ユーザー レベルでシークレットを保護することができます。 同じユーザー アカウントで実行されるタスクは、特権アクセスなしでシークレットにアクセスできます。

もう 1 つのシナリオは、プール スコープを使用して自動ユーザー アカウントでタスクを実行する場合の、Message Passing Interface (MPI) ファイル共有です。 MPI ファイル共有は、MPI タスク内のノードが同じファイルのデータを操作する必要がある場合に便利です。 ヘッド ノードでは、子ノードが同じ自動ユーザー アカウントで実行されている場合にアクセスできるファイル共有が作成されます。 

次のコード スニペットでは、Batch .NET でのタスクに対して自動ユーザーのスコープがプール スコープに設定されます。 タスクが標準のプール全体自動ユーザー アカウントで実行されるように、昇格レベルは省略されます。

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>名前付きユーザー アカウント

プールを作成するときに、名前付きユーザー アカウントを定義できます。 名前付きユーザー アカウントには、入力した名前とパスワードが設定されます。 名前付きユーザー アカウントの昇格のレベルを指定できます。 Linux ノードの場合は、SSH 秘密キーを指定することもできます。

名前付きユーザー アカウントはプール内のすべてのノードに存在し、それらのノードで実行されているすべてのタスクで使用できます。 1 つのプールに複数の名前付きユーザーを定義できます。 タスクまたはタスクのコレクションを追加するときに、プールで定義されたどの名前付きユーザー アカウントでタスクを実行するかを指定できます。

名前付きユーザー アカウントは、同じユーザー アカウントのジョブですべてのタスクを実行しながら、同時に他のジョブで実行中のタスクからそれらを分離する場合に役立ちます。 たとえば、ジョブごとに名前付きユーザーを作成して、その名前付きユーザー アカウントの下で各ジョブのタスクを実行します。 各ジョブでは、ジョブのタスク同士でシークレットを共有できますが、他のジョブで実行中のタスクとは共有できません。

名前付きのユーザー アカウントは、ファイル共有などの外部のリソースにアクセス許可を設定するタスクを実行するのに使用することもできます。 名前付きユーザー アカウントを使用してユーザー ID を制御し、そのユーザー ID を使用してアクセス許可を設定できます。  

名前付きユーザー アカウントでは、Linux ノード間でのパスワードのない SSH が有効になります。 マルチ インスタンスのタスクを実行する必要がある Linux ノードでは、名前付きユーザー アカウントを使用できます。 プール内の各ノードでは、プール全体で定義されているユーザー アカウントでタスクを実行できます。 マルチインスタンス タスクの詳細については、[MPI アプリケーションを実行するためのマルチインスタンス タスクの使用](batch-mpi.md)に関するページをご覧ください。

### <a name="create-named-user-accounts"></a>名前付きユーザー アカウントを作成する

Batch で名前付きユーザー アカウントを作成するには、ユーザー アカウントのコレクションをプールに追加します。 次のコード スニペットでは、.NET、Java、および Python で名前付きユーザー アカウントを作成する方法を示します。 これらのコード スニペットでは、プールで管理者と非管理者の両方の名前付きアカウントを作成する方法を説明します。 この例では、クラウド サービスの構成を使用してプールを作成し、Windows または Linux のプールを作成するときには、仮想マシンの構成で同じアプローチを使用します。

#### <a name="batch-net-example-windows"></a>Batch .NET の例 (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET の例 (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Batch Java の例

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch Python の例

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>特権アクセスによって、名前付きユーザー アカウントでタスクを実行する

昇格されたユーザーとしてタスクを実行するには、`Admin` に設定された **ElevationLevel** プロパティで作成された名前付きユーザー アカウントに、タスクの**UserIdentity** プロパティを設定します。

このコード スニペットでは、タスクを名前付きユーザー アカウントで実行する必要があることを指定します。 この名前付きユーザー アカウントは、プールの作成時にプールで定義されました。 このケースでは、名前付きユーザー アカウントは管理者アクセス許可で作成されています。

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>コードを最新の Batch クライアント ライブラリに更新する

Batch サービス バージョン 2017-01-01.4.0 では、以前のバージョンで利用可能な **runElevated** プロパティが **userIdentity** プロパティに置き換えられる、互換性を損ねる変更が行われています。 次の表は、クライアント ライブラリの以前のバージョンからコードを更新するのに使用できる、単純なマッピングを提供します。

### <a name="batch-net"></a>Batch .NET

| コードが次の場合                  | 次のように更新                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` の指定なし | 更新の必要なし                                                                                               |

### <a name="batch-java"></a>Batch Java

| コードが次の場合                      | 次のように更新                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` の指定なし | 更新の必要なし                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| コードが次の場合                      | 次のように更新                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`、ここで <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`、ここで <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin))`             |
| `run_elevated` の指定なし | 更新の必要なし                                                                                                                                  |


## <a name="next-steps"></a>次の手順

* Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。
