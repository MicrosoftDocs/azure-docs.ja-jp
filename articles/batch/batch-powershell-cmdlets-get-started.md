---
title: PowerShell の使用を開始する
description: Batch リソースの管理に使用できる Azure PowerShell コマンドレットの簡単な紹介。
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 2b51a2a7852df82625fb342bbbbc4a3a1cbf72a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685512"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>PowerShell コマンドレットで Batch リソースを管理する

Azure Batch PowerShell コマンドレットを使用すると、多くの一般的な Batch タスクを実行したり、そのスクリプトを作成したりできます。 この記事では、Batch アカウントを管理したり、プール、ジョブ、タスクといった Batch リソースを操作したりするときに使用できるコマンドレットについて説明します。

すべての Batch コマンドレットの一覧およびコマンドレットの詳細な構文については、 [Azure Batch コマンドレットのリファレンス](/powershell/module/az.batch)を参照してください。

最新のサービスや機能強化を活かすためにも、Azure PowerShell モジュールは定期的に更新することをお勧めします。

## <a name="prerequisites"></a>前提条件

- [Azure PowerShell モジュールをインストールして構成します](/powershell/azure/)。 特定の Azure Batch モジュール (プレリリース モジュールなど) をインストールする方法については、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az.Batch/)を参照してください。

- **Connect-AzAccount** コマンドレットを実行してサブスクリプションに接続します (Azure Batch コマンドレットは、Azure Resource Manager モジュールに付属しています)。

  ```powershell
  Connect-AzAccount
  ```

- **Batch プロバイダーの名前空間に登録します**。 この操作は、**サブスクリプションごとに 1 回** 実行するだけでかまいません。
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Batch アカウントとキーを管理する

### <a name="create-a-batch-account"></a>Batch アカウントを作成する

**New-AzBatchAccount** は、指定したリソース グループに Batch アカウントを作成します。 リソース グループがまだない場合は、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを実行して作成します。 **location** パラメーターで Azure リージョンのいずれか (例: "Central US") を指定します。 次に例を示します。

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

次に、リソース グループに Batch アカウントを作成します。 <*account_name*> にアカウントの名前を指定し、リソース グループの場所と名前を指定します。 Batch アカウントの作成は、完了までにしばらく時間がかかる場合があります。 次に例を示します。

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Batch アカウント名は、リソース グループの Azure リージョン内で重複しないこと、文字数が 3 ～ 24 文字であること、小文字と数字のみで構成されていることが必要になります。

### <a name="get-account-access-keys"></a>アカウントのアクセス キーを取得する

**Get-AzBatchAccountKeys** は、Azure Batch アカウントに関連付けられているアクセス キーを示します。 たとえば、作成したアカウントのプライマリ キーとセカンダリ キーを取得するには、次のように実行します。

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>新しいアクセス キーを生成する

**New-AzBatchAccountKey** は、Azure Batch アカウントの新しいプライマリ アカウント キーまたはセカンダリ アカウント キーを生成します。 たとえば、Batch アカウントの新しいプライマリ キーを生成するには、次のように入力します。

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> 新しいセカンダリ キーを生成するには、 **KeyType** パラメーターの "Secondary" を指定します。 プライマリ キーとセカンダリ キーは個別に再生成する必要があります。

### <a name="delete-a-batch-account"></a>Batch アカウントを削除する

**Remove-AzBatchAccount** は、Batch アカウントを削除します。 次に例を示します。

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

メッセージが表示されたら、削除するアカウントを確認します。 アカウントの削除は完了するまでに時間がかかる場合があります。

## <a name="create-a-batchaccountcontext-object"></a>BatchAccountContext オブジェクトを作成する

Batch リソースを管理するための認証は、共有キー認証または Azure Active Directory 認証を使用して行うことができます。 Batch PowerShell コマンドレットを使用して認証を行うにはまず、自分のアカウントの資格情報または ID を格納するための BatchAccountContext オブジェクトを作成します。 BatchAccountContext オブジェクトは、 **BatchContext** パラメーターを引数として受け取るコマンドレットに渡します。

### <a name="shared-key-authentication"></a>共有キー認証

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> 既定では、アカウントのプライマリ キーは認証に使用されますが、BatchAccountContext オブジェクトの **KeyInUse** プロパティを変更することで使用するキーを明示的に選択できます (例: `$context.KeyInUse = "Secondary"`)。

### <a name="azure-active-directory-authentication"></a>Azure Active Directory 認証

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Batch リソースを作成および変更する

Batch アカウントでリソースを作成するには、**New-AzBatchPool**、**New-AzBatchJob**、**New-AzBatchTask** などのコマンドレットを使用します。 既存のリソースのプロパティを更新するための対応する **Get-** コマンドレットと **Set-** コマンドレットがあり、Batch アカウントのリソースを削除するための **Remove-** コマンドレットがあります。

以下の例を見るとわかるように、これらのコマンドレットの多くは、その使用時に、BatchContext オブジェクトを渡すことに加え、リソースの詳細な設定を含んだオブジェクトを作成するか、引数として渡す必要があります。 その他の例については、各コマンドレットの詳細なヘルプを参照してください。

### <a name="create-a-batch-pool"></a>Create a Batch pool

Batch プールを作成または更新するときは、[構成](nodes-and-pools.md#configurations)を指定します。 プールは通常、仮想マシンの構成を使用して構成する必要があります。これを使用すると、[Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) に記載されているサポート対象の Linux または Windows VM イメージのいずれかを指定したり、準備したカスタム イメージを指定したりできます。 Cloud Services 構成プールでは、Windows コンピューティング ノードのみが提供されており、すべての Batch 機能がサポートされているわけではありません。

**New-AzBatchPool** を実行するときに、オペレーティング システムの設定を PSVirtualMachineConfiguration または PSCloudServiceConfiguration オブジェクトで渡します。 たとえば、以下のスニペットは、仮想マシンの構成を選び、Ubuntu Server 18.04-LTS のイメージを使用して、Standard_A1 サイズのコンピューティング ノードで Batch プールを作成しています。 ここでは、**VirtualMachineConfiguration** パラメーターに PSVirtualMachineConfiguration オブジェクトとして *$configuration* 変数を指定しています。 **BatchContext** パラメーターには、先ほど定義した *$context* 変数を BatchAccountContext オブジェクトとして指定しています。

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

新しいプール内のコンピューティング ノードの目標数は、自動スケーリングの式によって計算されます。 この例では、式は **$TargetDedicated=4** という簡単なものであり、プールのコンピューティング ノードの数が最大 4 であることを示します。

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>プール、ジョブ、タスク、およびその他の詳細のクエリ

Batch アカウントで作成されたエンティティを照会するには、**Get-AzBatchPool**、**Get-AzBatchJob**、**Get-AzBatchTask** などのコマンドレットを使用します。

### <a name="query-for-data"></a>データのクエリ

たとえば、**Get-AzBatchPools** を使用してプールを検索します。 既定では、これは、既に BatchAccountContext オブジェクトが *$context* に格納されていると仮定して、自分のアカウントのすべてのプールを照会します。

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>OData フィルターを使用する

**Filter** パラメーターを使用して OData フィルターを指定すると、関心のあるオブジェクトのみを検索できます。 たとえば、ID が "myPool" で始まるすべてのプールを検索できます。

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

このメソッドは、ローカルのパイプラインで “Where-Object” を使用するほど柔軟ではありません。 ただし、クエリは Batch サービスに直接送信されるため、すべてのフィルター処理がサーバー側で行われ、インターネットの帯域幅を節約できます。

### <a name="use-the-id-parameter"></a>ID パラメーターの使用

OData フィルターに代わる方法として、 **ID** パラメーターを使用します。 "myPool" という ID の特定のプールを照会するには:

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

**Id** パラメーターは、完全 ID の検索のみをサポートし、ワイルドカードや OData 形式のフィルターはサポートしません。

### <a name="use-the-maxcount-parameter"></a>MaxCount パラメーターを使用する

既定では、各コマンドレットは最大で 1000 のオブジェクトを返します。 この制限に達した場合は、オブジェクトが少なくなるようにフィルターで絞り込むか、 **MaxCount** パラメーターを使用して明示的に最大値を設定してください。 次に例を示します。

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

上限を削除するには、 **MaxCount** を 0 以下に設定します。

### <a name="use-the-powershell-pipeline"></a>PowerShell パイプラインを使用する

Batch コマンドレットは、コマンドレット間でデータを送信するために PowerShell パイプラインを使用します。 これは、パラメーターを指定するのと同じ効果がありますが、複数のエンティティを簡単に操作できます。

たとえば、自分のアカウントのすべてのタスクを検索して表示します。

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

プール内のすべてのコンピューティング ノードを再起動します。

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>アプリケーション パッケージの管理

[アプリケーション パッケージ](batch-application-packages.md)を利用すると、プール内のコンピューティング ノードにアプリケーションを簡単にデプロイできます。 Batch PowerShell コマンドレットを使用して、Batch アカウントのアプリケーション パッケージをアップロードおよび管理し、パッケージ バージョンをコンピューティング ノードにデプロイできます。

> [!IMPORTANT]
> アプリケーション パッケージを使用するには、お使いの Batch アカウントに Azure ストレージ アカウントをリンクする必要があります。

**作成** する:

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**追加** する:

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

アプリケーションの **既定のバージョン** を設定する:

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

アプリケーションのパッケージを **一覧表示** する:

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

アプリケーション パッケージを **削除** する:

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

アプリケーションを **削除** する:

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> アプリケーションを削除する前に、アプリケーションのアプリケーション パッケージのすべてのバージョンを削除する必要があります。 現在アプリケーション パッケージがあるアプリケーションを削除しようとすると、"競合" エラーが発生します。

### <a name="deploy-an-application-package"></a>アプリケーション パッケージをデプロイする

プールの作成時に、デプロイ用の 1 つ以上のアプリケーション パッケージを指定できます。 プールの作成時にパッケージを指定すると、ノードがプールに参加する際にパッケージが各ノードにデプロイされます。 ノードが再起動または再イメージ化されるときにも、パッケージがデプロイされます。

プールのノードがそこに参加するタイミングでアプリケーション パッケージをデプロイするには、`-ApplicationPackageReference` オプションを指定してプールを作成します。 まず、**PSApplicationPackageReference** オブジェクトを作成し、プールのコンピューティング ノードにデプロイするアプリケーション ID とパッケージのバージョンでオブジェクトを構成します。

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

次に、プールを作成し、`ApplicationPackageReferences` オプションの引数としてパッケージ参照オブジェクトを指定します。

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -VirtualMachineConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

アプリケーション パッケージについて詳しくは、「[Batch アプリケーション パッケージを使用したコンピューティング ノードへのアプリケーションのデプロイ](batch-application-packages.md)」をご覧ください。

### <a name="update-a-pools-application-packages"></a>プールに含まれるアプリケーション パッケージの更新

既存のプールに割り当てられているアプリケーションを更新するには、まず、必要なプロパティ (アプリケーション ID とパッケージのバージョン) を指定して PSApplicationPackageReference オブジェクトを作成します。

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

次に、Batch からプールを取得し、既存のパッケージをすべてクリアし、新しいパッケージ参照を追加し、新しいプール設定で Batch サービスを更新します。

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

これで、Batch サービスでプールのプロパティが更新されました。 ただし、プールのコンピューティング ノードに新しいアプリケーション パッケージを実際にデプロイするには、それらのノードを再起動するか、再イメージ化する必要があります。 次のコマンドを使用して、プール内のすべてのノードを再起動できます。

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> プール内のコンピューティング ノードに複数のアプリケーション パッケージをデプロイできます。 現在デプロイされているパッケージを置き換えるのではなく、アプリケーション パッケージを "追加" する場合は、上記の `$pool.ApplicationPackageReferences.Clear()` 行を省略します。

## <a name="next-steps"></a>次のステップ

- コマンドレットの詳細な構文と例については、[Azure Batch コマンドレットのリファレンス](/powershell/module/az.batch)に関するページを参照してください。
- [Batch アプリケーション パッケージを使用したコンピューティング ノードへのアプリケーションのデプロイ](batch-application-packages.md)方法について説明します。
