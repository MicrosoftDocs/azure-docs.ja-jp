---
title: コンピューティング ノードへのアプリケーション パッケージのデプロイ
description: Azure Batch のアプリケーション パッケージ機能を使用すると、Batch コンピューティング ノードにインストールされる複数のアプリケーションとバージョンを簡単に管理できます。
ms.topic: how-to
ms.date: 09/24/2020
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: b4c6ab99b0fa93e5fdea4256d6f232cc0fe4218e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033733"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Batch アプリケーション パッケージを使用したコンピューティング ノードへのアプリケーションのデプロイ

アプリケーション パッケージにより、Azure Batch ソリューションのコードが簡略化されると共に、タスクが実行されるアプリケーションの管理が容易になります。 アプリケーション パッケージを使用すると、タスクで実行される複数のバージョンのアプリケーション (そのサポート ファイルを含む) をアップロードして管理できます。 また、アップロードしたアプリケーションのうち 1 つ以上をプール内のコンピューティング ノードに自動的にデプロイできます。

アプリケーション パッケージの作成と管理を行う API は [Batch Management .NET](/dotnet/api/overview/azure/batch/management) ライブラリの一部です。 計算ノードでアプリケーション パッケージをインストールするための API は [Batch .NET](/dotnet/api/overview/azure/batch/client) ライブラリの一部です。 同等の機能は、他の言語の利用可能なバッチ API にあります。

この記事では、Azure portal にアプリケーション パッケージをアップロードして管理する方法について説明します。 また、[Batch .NET](/dotnet/api/overview/azure/batch/client) ライブラリを使用して、プールのコンピューティング ノードにそれらをインストールする方法についても説明します。

## <a name="application-package-requirements"></a>アプリケーション パッケージの要件

アプリケーション パッケージを使うには、[Azure Storage アカウントを Batch アカウントにリンク](#link-a-storage-account)する必要があります。

Batch アカウント内のアプリケーションとアプリケーション パッケージの数と最大アプリケーション パッケージ サイズには制限があります。 詳細については、「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

> [!NOTE]
> 2017 年 7 月 5 日より前に作成された Batch プールでは、アプリケーション パッケージはサポートされていません (Cloud Services の構成を使用して、2016 年 3 月 10 日より後に作成された場合を除く)。 ここで説明されているアプリケーション パッケージの機能は、以前のバージョンのサービスで利用できた "Batch Apps" 機能に代わるものです。

## <a name="understand-applications-and-application-packages"></a>アプリケーションとアプリケーション パッケージを理解する

Azure Batch では、" *アプリケーション* " という用語は、プール内のコンピューティング ノードに自動でダウンロード可能なバージョン付きバイナリのセットを指します。 アプリケーションには、アプリケーションのさまざまなバージョンを表す *アプリケーション パッケージ* が 1 つ以上含まれています。

各 *アプリケーション パッケージ* は、アプリケーション バイナリとサポート ファイルを含む .zip ファイルです。 .zip 形式のみがサポートされます。

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="アプリケーションとアプリケーション パッケージの概要を示す図。":::

アプリケーション パッケージはプールまたはタスク レベルで指定できます。

- **プールのアプリケーション パッケージ** は、プール内の " すべて " のノードにデプロイされます。 アプリケーションがデプロイされるのは、ノードがプールに参加するときと、ノードが再起動または再イメージ化されるときです。
  
    プールのアプリケーション パッケージは、プール内のすべてのノードがジョブのタスクを実行するときに適しています。 プールの作成時に、デプロイする 1 つ以上のアプリケーション パッケージを指定できます。 また、既存のプールのパッケージを追加または更新することもできます。 既存のプールに新しいパッケージをインストールするには、そのノードを再起動する必要があります。

- **タスクのアプリケーション パッケージ** は、タスクのコマンド ラインを実行する直前に、そのタスクを実行するようにスケジュールされたコンピューティング ノードのみにデプロイされます。 指定されたアプリケーション パッケージとバージョンが既にノードにある場合、それは再デプロイされず、既存のパッケージが使用されます。
  
    タスクのアプリケーション パッケージは、共有プール環境では便利です。この環境では、さまざまなジョブが 1 つのプールで実行され、ジョブが完了してもプールは削除されません。 ジョブ内のタスクがプール内のノードよりも少ない場合は、タスクのアプリケーション パッケージによりデータ転送を最小限に抑えることができます。アプリケーションはタスクが実行されるノードにのみデプロイされるためです。
  
    タスクのアプリケーション パッケージによるメリットがあるその他のシナリオとして、ごく少数のタスクを別にすると、大規模なアプリケーションを実行するジョブが挙げられます。 たとえば、タスク アプリケーションは、重い事前処理段階やマージ タスクに役立ちます。

アプリケーション パッケージでは、ノードにインストールする多数のリソース ファイルをプールの開始タスクで指定する必要がありません。 Azure Storage やノードで、アプリケーション ファイルの複数のバージョンを手動で管理する必要もありません。 さらに、ストレージ アカウント内のファイルへのアクセスを提供する [SAS URL](../storage/common/storage-sas-overview.md) の生成に苦労することもありません。 Batch は、バックグラウンドで Azure Storage と連携して、アプリケーション パッケージを保存し、コンピューティング ノードにデプロイします。

> [!NOTE]
> 開始タスクの合計サイズは、リソース ファイルと環境変数を含め、32,768 文字以下であることが必要です。 開始タスクがこの制限を超える場合は、別のオプションとしてアプリケーション パッケージを使用することができます。 リソース ファイルを含む .zip ファイル作成し、Azure Storage に BLOB としてアップロードし、開始タスクのコマンド ラインから解凍することもできます。

## <a name="upload-and-manage-applications"></a>アプリケーションのアップロードと管理

[Azure portal](https://portal.azure.com) または Batch Management API を使用して、Batch アカウントのアプリケーション パッケージを管理できます。 以下のセクションでは、ストレージ アカウントをリンクする方法と、Azure portal でアプリケーションとアプリケーション パッケージを追加および管理する方法について説明します。

### <a name="link-a-storage-account"></a>ストレージ アカウントをリンクする

アプリケーション パッケージを使用するには、お使いの Batch アカウントに [Azure Storage アカウント](accounts.md#azure-storage-accounts)をリンクする必要があります。 Batch サービスでは関連付けられたストレージ アカウントを使用して、アプリケーション パッケージを格納します。 Batch アカウント専用のストレージ アカウントを作成することをお勧めします。

ストレージ アカウントをまだ構成していない場合は、Batch アカウントで初めて **[アプリケーション]** を選択したときに、Azure portal に警告が表示されます。 ストレージ アカウントを Batch アカウントにリンクするには、 **[警告]** ウィンドウで **[ストレージ アカウント]** を選択します。その後、 **[ストレージ アカウント]** をもう一度選択します。

2 つのアカウントをリンクした後、Batch は、リンクされたストレージ アカウントに保存されているパッケージをコンピューティング ノードに自動的にデプロイできるようになります。

> [!IMPORTANT]
> アプリケーション パッケージは、[ファイアウォール規則](../storage/common/storage-network-security.md)で構成されている Azure Storage アカウントでは、あるいは **[階層構造の名前空間]** が **[有効]** に設定されている場合には使用できません。

Batch サービスは、Azure Storage を使ってアプリケーション パッケージをブロック BLOB として格納します。 ブロック BLOB データの[通常料金を課金](https://azure.microsoft.com/pricing/details/storage/)され、各パッケージのサイズは最大ブロック BLOB サイズを超えることはできません。 詳しくは、「[Azure Storage のスケーラビリティおよびストレージ アカウントのパフォーマンスのターゲット](../storage/blobs/scalability-targets.md)」を参照してください。 コストを最小限に抑えるために、必ず、アプリケーション パッケージのサイズと数を考慮し、非推奨のパッケージを定期的に削除してください。

### <a name="view-current-applications"></a>現在のアプリケーションの表示

Batch アカウントのアプリケーションを表示するには、左側のナビゲーション メニューで **[アプリケーション]** を選択します。

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Azure portal の [アプリケーション] メニュー項目のスクリーンショット。":::

このメニュー オプションを選択すると、 **[アプリケーション]** ウィンドウが開きます。 このウィンドウには、アカウント内に存在する各アプリケーションの ID と以下のプロパティが表示されます。

- **[パッケージ]** : このアプリケーションに関連するバージョン数。
- **[既定のバージョン]** : 該当する場合は、アプリケーションのデプロイ時にバージョンが指定されていない場合にインストールされるアプリケーションのバージョン。
- **[更新を許可する]** : パッケージの更新と削除を許可するかどうかを指定します。

計算ノード上のアプリケーション パッケージの[ファイル構造](files-and-directories.md)を確認するには、Azure portal で Batch アカウントに移動します。 **[プール]** を選択します。 次に、計算ノードが含まれるプールを選択します。 その後、アプリケーション パッケージがインストールされている計算ノードを選択し、**applications** フォルダーを開きます。

### <a name="view-application-details"></a>アプリケーションの詳細の表示

アプリケーションの詳細を表示するには、 **[アプリケーション]** ウィンドウでそれを選択します。 ご利用のアプリケーション用に次の設定を構成できます。

- **[更新を許可する]** : アプリケーション パッケージを [更新または削除](#update-or-delete-an-application-package)できるかどうかを示します。 既定値は **[はい]** です。 **[いいえ]** に設定した場合、既存のアプリケーション パッケージを更新または削除することはできませんが、新しいアプリケーション パッケージのバージョンを追加することはできます。
- **[既定のバージョン]** : アプリケーションをデプロイするときに、バージョンが指定されていない場合に使用する既定のアプリケーション パッケージ。
- **表示名**:アプリケーションに関する情報を表示するときに、Batch ソリューションで使用できるフレンドリ名。 たとえば、この名前は、Batch を通じて顧客に提供するサービスの UI で使用できます。

### <a name="add-a-new-application"></a>新しいアプリケーションの追加

新しいアプリケーションを作成するには、アプリケーション パッケージを追加し、一意のアプリケーション ID を指定します。

ご利用の Batch アカウントで、 **[アプリケーション]** 、 **[追加]** の順に選択します。

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Azure portal での新しいアプリケーションの作成プロセスのスクリーンショット。":::

次の情報を入力します。

- **アプリケーション ID**:新しいアプリケーションの ID。
- **バージョン**: アップロードするアプリケーション パッケージのバージョン。
- **アプリケーション パッケージ**:アプリケーションの実行に必要なアプリケーション バイナリとサポート ファイルを含む .zip ファイル。

入力する **[アプリケーション ID]** と **[バージョン]** では、これらの要件に従う必要があります。

- Windows ノードでは、ID には英数字、ハイフン、アンダースコアの任意の組み合わせを使用できる。 Linux ノードでは、英数字とアンダースコアのみ使用できる。
- 64 文字を超えることはできない。
- Batch アカウント内で一意にする必要がある。
- ID では大文字と小文字が維持され、区別はされない。

準備ができたら、 **[送信]** を選択します .zip ファイルが Azure Storage アカウントにアップロードされた後、ポータルに通知が表示されます。 アップロードするファイルのサイズやネットワーク接続の速度によっては、時間がかかることがあります。

### <a name="add-a-new-application-package"></a>新しいアプリケーション パッケージの追加

既存のアプリケーションに対してアプリケーション パッケージ バージョンを追加するには、ご利用の Batch アカウントの **[アプリケーション]** セクションでアプリケーションを選び、 **[追加]** を選択します。

新しいアプリケーションの場合と同様に、新しいパッケージの **[バージョン]** を指定して、 **[アプリケーション パッケージ]** フィールドの .zip ファイルをアップロードし、 **[送信]** を選択します。

### <a name="update-or-delete-an-application-package"></a>アプリケーション パッケージの更新または削除

既存のアプリケーション パッケージを更新または削除するには、Batch アカウントの **[アプリケーション]** セクションでアプリケーションを選択します。 変更するアプリケーション パッケージの行の省略記号を選択し、実行する操作を選びます。

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Azure portal のアプリケーション パッケージの更新および削除オプションを示すスクリーンショット。":::

**[更新]** を選択した場合は、新しい .zip ファイルをアップロードできます。 これにより、そのバージョン用にアップロードした前の .zip ファイルが置き換えられます。

**[削除]** を選択した場合は、そのバージョンの削除を確認するメッセージが表示されます。 **[OK]** を選択すると、Batch によって Azure Storage アカウントから .zip ファイルが削除されます。 アプリケーションの既定のバージョンを削除すると、そのアプリケーションの **[既定のバージョン]** の設定も削除されます。

## <a name="install-applications-on-compute-nodes"></a>コンピューティング ノードへのアプリケーションのインストール

Azure portal でアプリケーション パッケージを管理する方法を学習したので、ここではこれらをコンピューティング ノードにデプロイし、Batch タスクを使って実行する方法を説明します。

### <a name="install-pool-application-packages"></a>プールのアプリケーション パッケージのインストール

プール内のすべてのコンピューティング ノードにアプリケーション パッケージをインストールするには、対象のプールに対してアプリケーション パッケージへの " 参照 " を 1 つ以上指定します。 プールに指定したアプリケーション パッケージは、プールに参加した計算ノードや再起動または再イメージ化された各ノードにインストールされます。

Batch .NET では、新しいプールの作成時に、または既存のプールに対して、1 つまたは複数の [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) を指定します。 [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) クラスは、プールの計算ノードにインストールするアプリケーション ID とバージョンを指定します。

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> アプリケーション パッケージのデプロイが失敗した場合、そのノードは、Batch サービスによって[使用不可](/dotnet/api/microsoft.azure.batch.computenode.state)としてマークされます。そのノードに対しては、タスクの実行がスケジュールされません。 この場合、ノードを再起動してパッケージのデプロイを再開します。 ノードを再起動すると、そのノードでのタスクのスケジュールももう一度有効になります。

### <a name="install-task-application-packages"></a>タスクのアプリケーション パッケージのインストール

プールと同様、アプリケーション パッケージへの " 参照 " を指定します。 タスクがノード上で実行するようにスケジュールされると、タスクのコマンド ラインが実行される直前に、パッケージがダウンロードされ、展開されます。 指定したパッケージとバージョンが既にノードにインストールされている場合は、パッケージがダウンロードされず、既存のパッケージが使用されます。

タスクのアプリケーション パッケージをインストールするには、タスクの [CloudTask.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) プロパティを構成します。

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>インストールしたアプリケーションの実行

プールまたはタスクに指定したパッケージがダウンロードされ、ノードの `AZ_BATCH_ROOT_DIR` 内にある名前付きディレクトリに展開されます。 Batch により、その名前付きディレクトリへのパスを含む環境変数も作成されます。 タスクのコマンド ラインは、ノード上のアプリケーションを参照するときにこの環境変数を使用します。

Windows ノードでは、この変数の形式は次のとおりです。

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux ノードでは、形式が若干異なります。 ピリオド (.)、ハイフン (-)、番号記号 (#) は、環境変数ではアンダースコアにフラット化されます。 また、アプリケーション ID の大文字と小文字は保持されることに注意してください。 次に例を示します。

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` と `version` は、デプロイに指定したアプリケーションとパッケージ バージョンに対応する値です。 たとえば、アプリケーション "*blender*" のバージョン 2.7 が Windows ノードにインストールされるように指定した場合、タスクのコマンド ラインでは、次の環境変数を使ってそのファイルにアクセスします。

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux ノードでは、次の形式で環境変数を指定します。 ピリオド (.)、ハイフン (-)、および番号記号 (#) をアンダースコアにフラット化し、アプリケーション ID の大文字と小文字を保持します。

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

アプリケーション パッケージをアップロードするときに、コンピューティング ノードにデプロイする既定のバージョンを指定できます。 アプリケーションの既定のバージョンを指定した場合、アプリケーションを参照するときにバージョンのサフィックスを省略できます。 アプリケーションの既定のバージョンは、「**アプリケーションのアップロードと管理**」に示すように、Azure Portal の [[アプリケーション]](#upload-and-manage-applications) ウィンドウで指定できます。

たとえば、"*blender*" アプリケーションの既定バージョンとして 2.7 を設定した場合、タスクは次の環境変数を参照するため、Windows ノードはバージョン 2.7 を実行します。

`AZ_BATCH_APP_PACKAGE_BLENDER`

次のコード スニペットは、 *blender* アプリケーションの既定のバージョンを起動する、タスクのコマンド ラインの例を示します。

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> コンピューティング ノードの環境設定の詳細については、[タスクの環境設定](jobs-and-tasks.md#environment-settings-for-tasks)に関する記事を参照してください。

## <a name="update-a-pools-application-packages"></a>プールに含まれるアプリケーション パッケージの更新

アプリケーション パッケージで構成済みの既存プールに対して、新しいパッケージを指定することができます。 そのため、次のようになります。

- Batch サービスは、プールに参加する新しいノードすべてと、再起動または再イメージ化される既存のノードに、新しく指定されたパッケージをインストールします。
- パッケージへの参照を更新したときに、既にプールに参加していたコンピューティング ノードに対し、新しいアプリケーション パッケージが自動でインストールされることはありません。 新しいパッケージを受け取るには、これらのコンピューティング ノードを再起動または再イメージ化する必要があります。
- 新しいパッケージがデプロイされると、作成された環境変数に新しいアプリケーション パッケージ参照が反映されます。

この例では、既存のプールに、[CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) の 1 つとして *blender* アプリケーションのバージョン 2.7 が構成されています。 プールのノードをバージョン 2.76b で更新するには、新しいバージョンで新しい [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) を指定し、変更をコミットします。

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

新しいバージョンを構成したので、Batch サービスはプールに参加する "新しい" ノードにバージョン 2.76b をインストールします。 プール内の " 既存 " のノードに 2.76b をインストールするには、ノードを再起動または再イメージ化します。 再起動されたノードでは、以前のパッケージ デプロイのファイルが保持されることにご注意ください。

## <a name="list-the-applications-in-a-batch-account"></a>Batch アカウント内のアプリケーションの一覧表示

[ApplicationOperations.ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) メソッドを使用して、Batch アカウント内のアプリケーションとそのパッケージを一覧表示できます。

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>次のステップ

- [Batch REST API](/rest/api/batchservice) も、アプリケーション パッケージの運用をサポートしています。 たとえば、インストールするパッケージを指定する方法については [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) 要素を、アプリケーション情報の取得方法については「[アプリケーション](/rest/api/batchservice/application)」を参照してください。
- [Batch Management .NET でプログラムを使用して Azure Batch アカウントとクォータを管理する](batch-management-dotnet.md)方法を学習してください。 [Batch Management .NET](/dotnet/api/overview/azure/batch/management) ライブラリで、Batch アプリケーションやサービス用のアカウント作成機能と削除機能を有効にできます。
