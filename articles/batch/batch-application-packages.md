---
title: コンピューティング ノードへのアプリケーション パッケージのインストール - Azure Batch | Microsoft Docs
description: Azure Batch のアプリケーション パッケージ機能を使用すると、Batch コンピューティング ノードにインストールされる複数のアプリケーションとバージョンを簡単に管理できます。
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9c9d6d13efaa07bff2a1eaabe05725a3257cf895
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70095688"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Batch アプリケーション パッケージを使用したコンピューティング ノードへのアプリケーションのデプロイ

Azure Batch のアプリケーション パッケージ機能を使用すると、タスク アプリケーションの管理と、プール内のコンピューティング ノードへのそのアプリケーションのデプロイが簡単になります。 アプリケーション パッケージを使用すると、タスクで実行される複数のバージョンのアプリケーション (そのサポート ファイルを含む) をアップロードして管理できます。 また、アップロードしたアプリケーションのうち 1 つ以上をプール内のコンピューティング ノードに自動的にデプロイできます。

この記事では、Azure Portal でアプリケーション パッケージをアップロードおよび管理する方法を学習します。 その後、[Batch .NET][api_net] ライブラリを使用して、プールのコンピューティング ノードにそれらをインストールする方法を学習します。

> [!NOTE]
> アプリケーション パッケージは、2017 年 7 月 5 日より後に作成されたすべての Batch プールでサポートされます。 これらは、プールがクラウド サービス構成を使って作成された場合にのみ、2016 年 3 月 10 日から 2017 年 7 月 5 日までの間に作成された Batch プールでサポートされます。 2016 年 3 月 10 日より前に作成された Batch プールは、アプリケーション パッケージをサポートしていません。
>
> アプリケーション パッケージの作成と管理を行う API は [Batch Management .NET][api_net_mgmt] ライブラリの一部です。 計算ノードでアプリケーション パッケージをインストールするための API は [Batch .NET][api_net] ライブラリの一部です。 同等の機能は、他の言語の利用可能なバッチ API にあります。 
>
> ここで説明されているアプリケーション パッケージの機能は、以前のバージョンのサービスで利用できた "Batch Apps" 機能に代わるものです。

## <a name="application-package-requirements"></a>アプリケーション パッケージの要件
アプリケーション パッケージを使うには、[Azure Storage アカウントを Batch アカウントにリンク](#link-a-storage-account)する必要があります。

## <a name="about-applications-and-application-packages"></a>アプリケーションとアプリケーション パッケージについて
Azure Batch では、" *アプリケーション* " という用語は、プール内のコンピューティング ノードに自動でダウンロード可能なバージョン付きバイナリのセットを指します。 また、"*アプリケーション パッケージ*" という用語は、そうしたバイナリの "*特定のセット*" を指し、アプリケーションの特定の "*バージョン*" を表します。

![High-level diagram of applications and application packages][1]

### <a name="applications"></a>[アプリケーション]
Batch 内のアプリケーションは、1 つ以上のアプリケーション パッケージを含んでおり、アプリケーションの構成オプションを指定します。 たとえば、アプリケーションは、コンピューティング ノードにインストールする既定のアプリケーション パッケージのバージョンや、そのパッケージを更新または削除できるかどうかを指定します。

### <a name="application-packages"></a>アプリケーション パッケージ
アプリケーション パッケージは、タスクでアプリケーションを実行するために必要なアプリケーション バイナリとサポート ファイルを含む .zip ファイルです。 各アプリケーション パッケージは特定のバージョンのアプリケーションを表します。

アプリケーション パッケージはプール レベルおよびタスク レベルで指定できます。 プールまたはタスクを作成するときに、これらのパッケージの 1 つ以上と、必要に応じてバージョンを指定できます。

* **プールのアプリケーション パッケージ** は、プール内の " *すべて* " のノードにデプロイされます。 アプリケーションがデプロイされるのは、ノードがプールに参加するときと、ノードが再起動または再イメージ化されるときです。
  
    プールのアプリケーション パッケージは、プール内のすべてのノードがジョブのタスクを実行するときに適しています。 プールの作成時に 1 つ以上のアプリケーション パッケージを指定することも、既存のプールのパッケージを追加または更新することもできます。 既存のプールのアプリケーション パッケージを更新する場合は、新しいパッケージをインストールするために、そのノードを再起動する必要があります。
* **タスクのアプリケーション パッケージ** は、タスクのコマンド ラインを実行する直前に、そのタスクを実行するようにスケジュールされたコンピューティング ノードのみにデプロイされます。 指定されたアプリケーション パッケージとバージョンが既にノードにある場合、それは再デプロイされず、既存のパッケージが使用されます。
  
    タスクのアプリケーション パッケージは、共有プール環境では便利です。この環境では、さまざまなジョブが 1 つのプールで実行され、ジョブが完了してもプールは削除されません。 ジョブ内のタスクがプール内のノードよりも少ない場合は、タスクのアプリケーション パッケージによりデータ転送を最小限に抑えることができます。アプリケーションはタスクが実行されるノードにのみデプロイされるためです。
  
    タスクのアプリケーション パッケージによるメリットがあるその他のシナリオとして、ごく少数のタスクを別にすると、大規模なアプリケーションを実行するジョブが挙げられます。 たとえば、前処理段階またはマージ タスクがあります。この場合、前処理またはマージ アプリケーションは大規模で、タスクのアプリケーション パッケージの使用によるメリットがある可能性があります。

> [!IMPORTANT]
> Batch アカウント内のアプリケーションとアプリケーション パッケージの数と最大アプリケーション パッケージ サイズには制限があります。 これらの制限の詳細については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md) 」を参照してください。
> 
> 

### <a name="benefits-of-application-packages"></a>アプリケーション パッケージの利点
アプリケーション パッケージは、Batch ソリューションのコードを簡略化すると共に、タスクが実行されるアプリケーションの管理に必要なオーバーヘッドを軽減します。

アプリケーション パッケージでは、ノードにインストールする多数のリソース ファイルをプールの開始タスクで指定する必要がありません。 Azure Storage やノードで、アプリケーション ファイルの複数のバージョンを手動で管理する必要もありません。 さらに、ストレージ アカウント内のファイルへのアクセスを提供する [SAS URL](../storage/common/storage-dotnet-shared-access-signature-part-1.md) の生成に苦労することもありません。 Batch は、バックグラウンドで Azure Storage と連携して、アプリケーション パッケージを保存し、コンピューティング ノードにデプロイします。

> [!NOTE] 
> 開始タスクの合計サイズは、リソース ファイルと環境変数を含め、32,768 文字以下であることが必要です。 開始タスクがこの制限を超える場合は、別のオプションとしてアプリケーション パッケージの使用があります。 リソース ファイルを含む zip アーカイブを作成し、Azure Storage に BLOB としてアップロードし、開始タスクのコマンド ラインから解凍することもできます。 
>
>

## <a name="upload-and-manage-applications"></a>アプリケーションのアップロードと管理
[Azure portal][portal] または Batch Management API を使用して、Batch アカウントのアプリケーション パッケージを管理できます。 次のいくつかのセクションでは、まずストレージ アカウントのリンク方法を示してから、アプリケーションとパッケージの追加とポータルを使った管理について説明します。

### <a name="link-a-storage-account"></a>ストレージ アカウントのリンク
アプリケーション パッケージを使用するには、最初に [Azure Storage アカウント](batch-api-basics.md#azure-storage-account)を Batch アカウントにリンクする必要があります。 ストレージ アカウントをまだ構成していない場合は、Batch アカウントで初めて **[アプリケーション]** をクリックしたときに、Azure Portal に警告が表示されます。



![Azure Portal の「構成されているストレージ アカウントがありません」警告][9]

Batch サービスは関連付けられたストレージ アカウントを使って、アプリケーション パッケージを格納します。 2 つのアカウントをリンクした後、Batch は、リンクされたストレージ アカウントに保存されているパッケージをコンピューティング ノードに自動的にデプロイできるようになります。 ストレージ アカウントを Batch アカウントにリンクするには、 **[警告]** ウィンドウで **[ストレージ アカウント]** をクリックします。その後、 **[ストレージ アカウント]** をもう一度クリックします。

![Choose storage account blade in Azure portal][10]

Batch アカウントで使うストレージ アカウントは "*専用*" のものを作成することをお勧めします。ここでは、そのようにします。 ストレージ アカウントを作成したら、 **[ストレージ アカウント]** ウィンドウを使用して Batch アカウントにリンクすることができます。

> [!NOTE] 
> 現時点では、[ファイアウォール規則](../storage/common/storage-network-security.md)で構成されている Azure Storage アカウントでアプリケーション パッケージを使用することはできません。
> 

Batch サービスは、Azure Storage を使ってアプリケーション パッケージをブロック BLOB として格納します。 ブロック BLOB データの[通常料金を課金][storage_pricing]され、各パッケージのサイズは[最大ブロック BLOB サイズ](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets)を超えることはできません。 アプリケーション パッケージのサイズと数に気を配り、非推奨のパッケージを定期的に削除して、コストを最小限に抑えてください。
> 
> 

### <a name="view-current-applications"></a>現在のアプリケーションの表示
Batch アカウントに含まれているアプリケーションを表示するには、 **[Batch アカウント]** の表示中に、左側のメニューにある **[アプリケーション]** メニュー項目をクリックします。

![Applications tile][2]

このメニュー オプションを選択すると、 **[アプリケーション]** ウィンドウが開きます。

![List applications][3]

このウィンドウには、アカウント内に存在する各アプリケーションの ID と以下のプロパティが表示されます。

* **[パッケージ]** : このアプリケーションに関連するバージョン数。
* **[既定のバージョン]** : プールにアプリケーションを指定する際にバージョンを指定しない場合にインストールされるアプリケーション バージョン。 この設定はオプションです。
* **[更新を許可する]** : パッケージの更新、削除、追加を許可するかどうかを指定する値。 **[いいえ]** に設定されている場合は、アプリケーションのパッケージの更新と削除が無効になります。 新しいアプリケーション パッケージ バージョンの追加のみが可能です。 既定値は **[はい]** です。

コンピューティング ノード上のアプリケーション パッケージのファイル構造を確認するには、ポータルで Batch アカウントに移動します。 Batch アカウントから **プール**に移動します。 関心があるコンピューティング ノードを格納するプールを選択します。

![プール内のノード][13]

プールを選択したら、アプリケーション パッケージがインストールされているコンピューティング ノードに移動します。 そこから、アプリケーション パッケージの詳細が**アプリケーション**フォルダーに配置されます。 コンピューティング ノード上の追加のフォルダーには、開始タスク、出力ファイル、エラー出力などの他のファイルが含まれます。

![ノード上のファイル][14]

### <a name="view-application-details"></a>アプリケーションの詳細の表示
アプリケーションの詳細を表示するには、 **[アプリケーション]** ウィンドウでそのアプリケーションを選択します。

![Application details][4]

アプリケーションの詳細では、アプリケーションに関する以下の設定を構成することができます。

* **[更新を許可する]** : アプリケーション パッケージを更新または削除できるかどうかを指定します。 この記事の後半にある「アプリケーション パッケージの更新または削除」を参照してください。
* **[既定のバージョン]** : コンピューティング ノードにデプロイする既定のアプリケーション パッケージを指定します。
* **表示名**:たとえば、Batch ソリューションがアプリケーションの情報を表示するときに、Batch を介して顧客に提供するサービスの UI に使用できるわかりやすい名前を指定します。

### <a name="add-a-new-application"></a>新しいアプリケーションの追加
新しいアプリケーションを作成するには、アプリケーション パッケージを追加し、新しい一意のアプリケーション ID を指定します。 新しいアプリケーション ID を使って最初のアプリケーション パッケージを追加すると、新しいアプリケーションも作成されます。

**[アプリケーション]**  >  **[追加]** の順にクリックします。

![New application blade in Azure portal][5]

**[新しいアプリケーション]** ウィンドウには、新しいアプリケーションとアプリケーション パッケージの設定を指定するための次のフィールドがあります。

**アプリケーション ID**

このフィールドでは、標準の Azure Batch ID 検証ルールに従って、新しいアプリケーションの ID を指定します。 アプリケーション ID を指定するためのルールは次のとおりです。

* Windows ノードでは、ID には英数字、ハイフン、アンダースコアの任意の組み合わせを使用できる。 Linux ノードでは、英数字とアンダースコアのみ使用できる。
* 64 文字以内にする必要がある。
* Batch アカウント内で一意にする必要がある。
* 大文字と小文字が維持され、区別はされない。

**バージョン**

このフィールドでは、アップロードするアプリケーション パッケージのバージョンを指定します。 バージョン文字列は以下の検証ルールに従うようにします。

* Windows ノードでは、バージョン文字列には英数字、ハイフン、アンダースコア、ピリオドの任意の組み合わせを使用できる。 Linux ノードでは、バージョン文字列には英数字とアンダースコアのみ使用できる。
* 64 文字以内にする必要がある。
* アプリケーション内で一意にする必要がある。
* 大文字と小文字が維持され、区別はされない。

**[Application package (アプリケーション パッケージ)]**

このフィールドは、アプリケーションの実行に必要なアプリケーション バイナリとサポート ファイルを含む .zip ファイルを指定します。 **[ファイルの選択]** ボックスかフォルダー アイコンをクリックして、アプリケーションのファイルが格納されている .zip ファイルを参照して選択します。

ファイルを選択したら、 **[OK]** をクリックして Azure Storage へのアップロードを開始します。 アップロード操作が完了すると、ポータルに通知が表示されます。 アップロードするファイルのサイズやネットワーク接続の速度によっては、アップロードに時間がかかることもあります。

> [!WARNING]
> アップロード操作が完了するまで、 **[新しいアプリケーション]** ウィンドウを閉じないでください。 途中で閉じると、アップロード プロセスが停止します。
> 
> 

### <a name="add-a-new-application-package"></a>新しいアプリケーション パッケージの追加
既存のアプリケーションに対してアプリケーション パッケージ バージョンを追加するには、 **[アプリケーション]** ウィンドウでアプリケーションを選択し、 **[パッケージ]**  >  **[追加]** の順にクリックします。

![Add application package blade in Azure portal][8]

ご覧のように、フィールドは **[新しいアプリケーション]** ウィンドウのそれに一致しますが、 **[アプリケーション ID]** は無効になっています。 新しいアプリケーションの場合と同様に、新しいパッケージの**バージョン**を指定して、**アプリケーション パッケージ**の .zip ファイルを参照し、 **[OK]** をクリックしてパッケージをアップロードします。

### <a name="update-or-delete-an-application-package"></a>アプリケーション パッケージの更新または削除
既存のアプリケーション パッケージを更新または削除するには、対象アプリケーションの詳細を開き、 **[パッケージ]** をクリックします。次に、変更するアプリケーション パッケージの行の**省略記号**をクリックして、実行する操作を選択します。

![Update or delete package in Azure portal][7]

**Update**

**[更新]** をクリックすると、 **[パッケージの更新]** ウィンドウが表示されます。 このウィンドウは**新しいアプリケーション パッケージ**のウィンドウと似ていますが、パッケージ選択フィールドのみが有効になっていて、アップロードする新しい ZIP ファイルを指定できます。

![Update package blade in Azure portal][11]

**削除**

**[削除]** をクリックすると、当該パッケージ バージョンを削除するかどうかを確認するメッセージが表示され、承諾すると Batch がそのパッケージを Azure Storage から削除します。 アプリケーションの既定のバージョンを削除すると、そのアプリケーションの **既定のバージョン** の設定も削除されます。

![Delete application ][12]

## <a name="install-applications-on-compute-nodes"></a>コンピューティング ノードへのアプリケーションのインストール
Azure Portal でアプリケーション パッケージを管理する方法を確認したので、これらのパッケージをコンピューティング ノードにデプロイし、Batch タスクを使って実行する方法を説明します。

### <a name="install-pool-application-packages"></a>プールのアプリケーション パッケージのインストール
プール内のすべてのコンピューティング ノードにアプリケーション パッケージをインストールするには、対象のプールに対してアプリケーション パッケージへの " *参照* " を 1 つ以上指定します。 プールに指定したアプリケーション パッケージは、コンピューティング ノードがプールに参加したときや再起動されたとき、再イメージ化されたときに各ノードにインストールされます。

Batch .NET では、新しいプールの作成時に、または既存のプールに対して、1 つ以上の [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] を追加します。 [ApplicationPackageReference][net_pkgref] クラスは、プールの計算ノードにインストールするアプリケーション ID とバージョンを指定します。

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
> アプリケーション パッケージのデプロイが何らかの理由で失敗した場合、そのノードは、Batch サービスによって[使用不可][net_nodestate]としてマークされます。そのノードに対しては、タスクの実行がスケジュールされません。 この場合、パッケージのデプロイを再開するには、ノードを**再起動**する必要があります。 ノードを再起動すると、そのノードでのタスクのスケジュールももう一度有効になります。
> 
> 

### <a name="install-task-application-packages"></a>タスクのアプリケーション パッケージのインストール
プールと同様、アプリケーション パッケージへの " *参照* " を指定します。 タスクがノード上で実行するようにスケジュールされると、タスクのコマンド ラインが実行される直前に、パッケージがダウンロードされ、展開されます。 指定したパッケージとバージョンが既にノードにインストールされている場合は、パッケージがダウンロードされず、既存のパッケージが使用されます。

タスクのアプリケーション パッケージをインストールするには、タスクの [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref] プロパティを構成します。

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

Linux ノードでは、形式が若干異なります。 ピリオド (.)、ハイフン (-)、番号記号 (#) は、環境変数ではアンダースコアにフラット化されます。 また、アプリケーション ID の大文字と小文字は保持されることに注意してください。 例:

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
> コンピューティング ノードの環境設定の詳細については、[Batch 機能の概要](batch-api-basics.md)に関するページの「[タスクの環境設定](batch-api-basics.md#environment-settings-for-tasks)」を参照してください。
> 
> 

## <a name="update-a-pools-application-packages"></a>プールに含まれるアプリケーション パッケージの更新
アプリケーション パッケージで構成済みの既存プールに対して、新しいパッケージを指定することができます。 プールに対して新しいパッケージ参照を指定すると、次のようになります。

* Batch サービスは、プールに参加する新しいノードすべてと、再起動または再イメージ化される既存のノードに、新しく指定されたパッケージをインストールします。
* パッケージへの参照を更新したときに、既にプールに参加していたコンピューティング ノードに対し、新しいアプリケーション パッケージが自動でインストールされることはありません。 新しいパッケージを受け取るには、これらのコンピューティング ノードを再起動または再イメージ化する必要があります。
* 新しいパッケージがデプロイされると、作成された環境変数に新しいアプリケーション パッケージ参照が反映されます。

この例では、既存のプールに、[CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] の 1 つとして *blender* アプリケーションのバージョン 2.7 が構成されています。 プールのノードをバージョン 2.76b で更新するには、新しいバージョンで新しい [ApplicationPackageReference][net_pkgref] を指定し、変更をコミットします。

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

新しいバージョンを構成したので、Batch サービスはプールに参加する "*新しい*" ノードにバージョン 2.76b をインストールします。 プール内の " *既存* " のノードに 2.76b をインストールするには、ノードを再起動または再イメージ化します。 再起動されたノードは、以前のパッケージ デプロイのファイルを保持することにご注意ください。

## <a name="list-the-applications-in-a-batch-account"></a>Batch アカウント内のアプリケーションの一覧表示
[ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] メソッドを使用して、Batch アカウント内のアプリケーションとパッケージを一覧表示できます。

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

## <a name="wrap-up"></a>まとめ
アプリケーション パッケージを使用すると、顧客がジョブ用のアプリケーションを選択するのを支援したり、Batch を有効にしたサービスでジョブを処理する場合の正確なバージョン指定を行ったりすることができます。 さらに、顧客がサービスに自前のアプリケーションをアップロードし、トラッキングするための環境も用意できます。

## <a name="next-steps"></a>次の手順
* [Batch REST API][api_rest] も、アプリケーション パッケージの運用をサポートしています。 たとえば、REST API を使用したインストール パッケージの指定方法については、[アカウントへのプールの追加][rest_add_pool]に関する記事の [applicationPackageReferences][rest_add_pool_with_packages] 要素を参照してください。 Batch REST API を使用したアプリケーション情報の取得方法の詳細については、「[アプリケーション][rest_applications]」をご覧ください。
* [Batch Management .NET でプログラムを使用して Azure Batch アカウントとクォータを管理する](batch-management-dotnet.md)方法を学習してください。 [Batch Management .NET][api_net_mgmt] ライブラリで、Batch アプリケーションやサービス用のアカウント作成機能と削除機能を有効にできます。

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Application packages high-level diagram"
[2]: ./media/batch-application-packages/app_pkg_02.png "Applications tile in Azure portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Applications blade in Azure portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Application details blade in Azure portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "New application blade in Azure portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Update or delete packages drop-down in Azure portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "New application package blade in Azure portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "No linked Storage account alert"
[10]: ./media/batch-application-packages/app_pkg_10.png "Choose storage account blade in Azure portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Update package blade in Azure portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Delete package confirmation dialog in Azure portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Azure portal でノード情報を計算します。"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Azure portal に表示されるコンピューティング ノード上のファイル"
