---
title: Azure ファイル共有を作成する
titleSuffix: Azure Files
description: Azure portal、PowerShell、Azure CLI を使用して Azure ファイル共有を作成する方法。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 236134887728ebc3dd4d03fa4c9d9d450b39eac2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930674"
---
# <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
Azure ファイル共有を作成するには、その使用方法について 3 つの質問に答える必要があります。

- **Azure ファイル共有のパフォーマンス要件はどのようなものですか?**  
    Azure Files には、ハード ディスクベース (HDD ベース) のハードウェアでホストされる Standard ファイル共有と、ソリッドステート ディスクベース (SSD ベース) ハードウェアでホストされる Premium ファイル共有が用意されています。

- **どのくらいのサイズのファイル共有が必要ですか?**  
    Standard ファイル共有は最大 100 TiB にまたがることができますが、この機能は既定では有効になっていません。5 TiB を超えるファイル共有が必要な場合は、ストレージ アカウントに対して大きいファイルの共有機能を有効にする必要があります。 Premium ファイル共有は、特別な設定を行わなくても最大 100 TiB にまたがることができますが、Standard ファイル共有のような従量課金制ではなく、Premium ファイル共有がプロビジョニングされます。 つまり、必要よりずっと多いファイル共有がプロビジョニングされることで、ストレージの総コストが増加します。

- **Azure ファイル共有の冗長性の要件はどのようなものですか?**  
    Standard ファイル共有では、ローカル冗長 (LRS) ストレージ、ゾーン冗長 (ZRS) ストレージ、geo 冗長 (GRS) ストレージ、または geo ゾーン冗長 (GZRS) ストレージが用意されています。ただし、大きいファイルの共有機能は、ローカル冗長およびゾーン冗長のファイル共有でのみサポートされます。 Premium ファイル共有では、どのような種類の geo 冗長性もサポートされていません。

    Premium ファイル共有は、ストレージ アカウントを提供しているほとんどのリージョンにおいて、ローカル冗長で利用できます。また、一部のリージョンではゾーン冗長で利用できます。 ご自分のリージョンで現在 Premium ファイル共有を使用できるかどうかを見つけるには、Azure の [[リージョン別の利用可能な製品]](https://azure.microsoft.com/global-infrastructure/services/?products=storage) ページを参照してください。 ZRS をサポートするリージョンの詳細については、[Azure Storage の冗長性](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関する記事を参照してください。

これら 3 つの選択の詳細については、「[Azure Files のデプロイの計画](storage-files-planning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
- この記事では、既に Azure サブスクリプションが作成されていることを前提としています。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
- Azure PowerShell を使用する場合は、[最新バージョンをインストールしてください](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
Azure ファイル共有は、ストレージの共有プールを表す最上位オブジェクトである "*ストレージ アカウント*" にデプロイされます。 このストレージのプールは、複数のファイル共有をデプロイするのに使用できます。 

Azure では、お客様のさまざまなストレージ シナリオに対して複数のストレージ アカウントの種類がサポートされていますが、Azure Files には主に 2 種類のストレージ アカウントがあります。 作成する必要があるストレージ アカウントの種類は、Standard ファイル共有または Premium ファイル共有のどちらを作成するかによって異なります。 

- **General Purpose バージョン 2 (GPv2) ストレージ アカウント**:GPv2 ストレージ アカウントを使うと、Standard のハード ディスク ベース (HDD ベース) のハードウェアに、Azure ファイル共有をデプロイできます。 GPv2 ストレージ アカウントでは、Azure ファイル共有を格納するだけでなく、BLOB コンテナー、キュー、テーブルなどの他のストレージ リソースを格納することもできます。 

- **FileStorage ストレージ アカウント**:FileStorage ストレージ アカウントを使うと、Premium のソリッドステート ディスク ベース (SSD ベース) のハードウェアに、Azure ファイル共有をデプロイできます。 FileStorage アカウントは、Azure ファイル共有を格納するためにのみ使用できます。FileStorage アカウントでその他のストレージ リソース (BLOB コンテナー、キュー、テーブルなど) をデプロイすることはできません。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
Azure portal を使用してストレージ アカウントを作成するには、ダッシュボードから **[+ リソースの作成]** を選択します。 その結果表示される Azure Marketplace の検索ウィンドウで、**ストレージ アカウント**を検索し、検索結果を選択します。 これにより、ストレージ アカウントの概要ページが表示されます。ストレージ アカウントの作成ウィザードを続行するには、 **[作成]** を選択します。

![ブラウザーでのストレージ アカウントの簡易作成オプションのスクリーンショット](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>[基本] セクション
ストレージ アカウントを作成するために完了する最初のセクションには、 **[基本]** というラベルが付いています。 これには、ストレージ アカウントを作成するために必要なすべてのフィールドが含まれています。 GPv2 ストレージ アカウントを作成するには、確実に **[パフォーマンス]** オプション ボタンを *[Standard]* に設定し、 **[アカウントの種類]** ドロップダウン リストで *[StorageV2 (汎用 v2)]* を選択します。

![[Standard] が選択された [パフォーマンス] オプション ボタンと、[StorageV2] が選択された [アカウントの種類] のスクリーンショット](media/storage-how-to-create-file-share/create-storage-account-1.png)

FileStorage ストレージ アカウントを作成するには、確実に **[パフォーマンス]** オプション ボタンを *[Premium]* に設定し、 **[アカウントの種類]** ドロップダウン リストで *[FileStorage]* を選択します。

![[Premium] が選択された [パフォーマンス] オプション ボタンと、[FileStorage] が選択された [アカウントの種類] のスクリーンショット](media/storage-how-to-create-file-share/create-storage-account-2.png)

その他の基本フィールドは、ストレージ アカウントの選択とは無関係です。
- **サブスクリプション**:デプロイ先のストレージ アカウントのサブスクリプション。 
- **[リソース グループ]** :デプロイ先のストレージ アカウントのリソース グループ。 新しいリソース グループを作成することも、既存のリソース グループを使用することもできます。 リソース グループは、Azure サービスをグループ化するための論理コンテナーです。 ストレージ アカウントを作成するときに、新しいリソース グループを作成するか、既存のリソース グループを使用するかを選択できます。
- **ストレージ アカウント名**: 作成するストレージ アカウントリソースの名前。 この名前はグローバルに一意である必要がありますが、任意の名前を使用できます。 SMB 経由で Azure ファイル共有をマウントするときに、ストレージ アカウント名がサーバー名として使用されます。
- **[場所]** :デプロイ先のストレージ アカウントのリージョン。 これは、リソース グループに関連付けられているリージョン、または使用可能なその他のリージョンを指定できます。
- **[Replication]\(レプリケーション\)** :このフィールドはレプリケーションというラベルが付いていますが、実際には**冗長性**を意味します。必要な冗長レベルは、ローカル冗長 (LRS)、ゾーン冗長 (ZRS)、geo 冗長性 (GRS)、geo ゾーン冗長性です。 このドロップダウン リストには、Azure ファイル共有には適用されない読み取りアクセス geo 冗長 (RA-GRS) と読み取りアクセス geo ゾーン冗長 (RA-GZRS) も含まれています。これらが選択されたストレージ アカウントで作成されたファイル共有は、実際にはそれぞれ geo 冗長または geo ゾーン冗長のいずれかになります。 リージョンまたは選択したストレージアカウントの種類によっては、一部の冗長性オプションが許可されない場合があります。
- **アクセス層**:このフィールドは Azure Files には適用されないため、いずれかのオプション ボタンを選択できます。

#### <a name="the-networking-blade"></a>[ネットワーク] ブレード
[ネットワーク] セクションでは、ネットワーク オプションを構成できます。 これらの設定は、ストレージ アカウントを作成する場合は省略可能であり、必要に応じて後で構成できます。 これらのオプションの詳細については、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を参照してください。

#### <a name="the-advanced-blade"></a>[詳細] ブレード
[詳細] セクションには、Azure ファイル共有のいくつかの重要な設定が含まれています。

- **[安全な転送が必須]** :このフィールドは、ストレージ アカウントへの通信でストレージ アカウントが転送中の暗号化を必要とするかどうかを示します。 これは有効なままにしておくことをお勧めしますが、SMB 2.1 のサポートが必要な場合は、これを無効にする必要があります。 暗号化を無効にする場合は、ストレージ アカウントのアクセスを、サービス エンドポイントとプライベート エンドポイントのいずれかまたは両方を持つ仮想ネットワークに制限することをお勧めします。
- **[大きいファイルの共有]** :このフィールドでは、ストレージ アカウントに対して最大 100 TiB にまたがるファイル共有を有効にします。 この機能を有効にすると、ストレージ アカウントがローカル冗長およびゾーン冗長ストレージのオプションのみに制限されます。 GPv2 ストレージ アカウントを大きいファイルの共有に対して有効にすると、大きいファイルの共有機能を無効にすることはできません。 すべての Premium ファイル共有が 100 TiB までスケールアップできるため、FileStorage ストレージ アカウント (Premium ファイル共有のストレージ アカウント) にはこのオプションはありません。 

![Azure Files に適用される重要な詳細設定のスクリーンショット](media/storage-how-to-create-file-share/create-storage-account-3.png)

[詳細] タブで使用できるその他の設定 (BLOB の論理的な削除、Azure Data Lake Storage Gen 2 の階層型名前空間、BLOB ストレージ用の NFSv3) は、Azure Files には適用されません。

#### <a name="tags"></a>Tags
タグは名前と値のペアで、同じタグを複数のリソースやリソース グループに適用することでリソースを分類したり、統合した請求を表示したりできるようにします。 これらは省略可能であり、ストレージ アカウントの作成後に適用できます。

#### <a name="review--create"></a>確認と作成
ストレージ アカウントを作成する最後の手順は、 **[確認と作成]** タブで **[作成]** ボタンを選択することです。ストレージ アカウントのすべての必須フィールドが入力されていないと、このボタンは使用できません。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell を使用してストレージ アカウントを作成するには、`New-AzStorageAccount` コマンドレットを使用します。 このコマンドレットには多くのオプションがあり、必要なオプションのみが表示されます。 詳細オプションの詳細については、[`New-AzStorageAccount` コマンドレットのドキュメント](/powershell/module/az.storage/new-azstorageaccount)を参照してください。

ストレージ アカウントとそれに続くファイル共有の作成を簡略化するために、いくつかのパラメーターを変数に格納します。 変数の内容は任意の値に置き換えることができますが、ストレージ アカウント名はグローバルに一意である必要があることに注意してください。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Standard Azure ファイル共有を格納できるストレージ アカウントを作成するには、次のコマンドを使用します。 `-SkuName` パラメーターは、必要な冗長性の種類に関連しています。geo 冗長または geo ゾーン冗長ストレージ アカウントが必要な場合は、`-EnableLargeFileShare` パラメーターも削除する必要があります。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Premium Azure ファイル共有を格納できるストレージ アカウントを作成するには、次のコマンドを使用します。 `-SkuName` パラメーターが、`Premium` とローカル冗長 (`LRS`) の必要な冗長レベルの両方を含むように変更されていることに注意してください。 `-Kind` パラメーターは `StorageV2` ではなく `FileStorage` です。これは、GPv2 ストレージ アカウントではなく、FileStorage ストレージ アカウントに Premium ファイル共有を作成する必要があるためです。

```azurepowershell-interactive
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI を使用してストレージ アカウントを作成するには、az storage account create コマンドを使用します。 このコマンドには多くのオプションがあり、必要なオプションのみが表示されます。 詳細オプションの詳細については、[`az storage account create` コマンドのドキュメント](/cli/azure/storage/account)を参照してください。

ストレージ アカウントとそれに続くファイル共有の作成を簡略化するために、いくつかのパラメーターを変数に格納します。 変数の内容は任意の値に置き換えることができますが、ストレージ アカウント名はグローバルに一意である必要があることに注意してください。

```azurecli-interactive
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Standard Azure ファイル共有を格納できるストレージ アカウントを作成するには、次のコマンドを使用します。 `--sku` パラメーターは、必要な冗長性の種類に関連しています。geo 冗長または geo ゾーン冗長ストレージ アカウントが必要な場合は、`--enable-large-file-share` パラメーターも削除する必要があります。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Premium Azure ファイル共有を格納できるストレージ アカウントを作成するには、次のコマンドを使用します。 `--sku` パラメーターが、`Premium` とローカル冗長 (`LRS`) の必要な冗長レベルの両方を含むように変更されていることに注意してください。 `--kind` パラメーターは `StorageV2` ではなく `FileStorage` です。これは、GPv2 ストレージ アカウントではなく、FileStorage ストレージ アカウントに Premium ファイル共有を作成する必要があるためです。

```azurecli-interactive
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>ファイル共有の作成
ストレージ アカウントを作成したら、後はファイル共有を作成するだけです。 このプロセスは、Premium ファイル共有と Standard ファイル共有のどちらを使用しているかに関係なく、ほぼ同じです。 主な違いは、**クォータ** とそれが表すものです。

Standard ファイル共有の場合、それはエンド ユーザーがアクセスできない Azure ファイル共有の上限です。 Standard ファイル共有のクォータの主な目的は、予算です。つまり、"このファイル共有は、この点を超えて拡張させない" ということです。 クォータが指定されていない場合、Standard ファイル共有は最大 100 TiB (ストレージ アカウントに対して大きいファイルの共有プロパティが設定されていない場合は 5 TiB) にまたがることができます。

Premium ファイル共有の場合、クォータは**プロビジョニング済みのサイズ**を意味するようにオーバーロードされます。 実際の使用量に関係なく、プロビジョニング済みのサイズが課金される容量です。 Premium ファイル共有をプロビジョニングする場合は、次の 2 つの要素を考慮する必要があります。1) 領域使用率の観点から共有の将来の成長、および 2) ワークロードに必要な IOPS。 プロビジョニング済みの GiB ごとに、追加の予約済み IOPS とバースト IOPS が付与されます。 Premium ファイル共有を計画する方法の詳細については、[Premium ファイル共有のプロビジョニング](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)に関するセクションを参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
ストレージ アカウントを作成したばかりの場合は、[デプロイ] 画面から **[リソースに移動]** を選択して移動できます。 ストレージ アカウントを既に作成してある場合は、それを含むリソース グループを介して移動できます。 ストレージ アカウントで、 **[ファイル共有]** というラベルの付いたタイルを選択します (ストレージ アカウントの目次を介して**ファイル共有**に移動することもできます)。

![[ファイル共有] タイルのスクリーンショット](media/storage-how-to-create-file-share/create-file-share-1.png)

ファイル共有リストには、このストレージ アカウントで以前に作成したファイル共有がすべて表示されます。ファイル共有がまだ作成されていない場合は、空のテーブルが表示されます。 **[+ ファイル共有]** を選択して、新しいファイル共有を作成します。

新しい [ファイル共有] ブレードが画面に表示されます。 新しいファイル共有ブレードのフィールドをすべて入力して、ファイル共有を作成します。

- **[名前]** : 作成するファイルの名前。
- **[クォータ]** :Standard ファイル共有のファイル共有のクォータ。Premium ファイル共有のプロビジョニング済みのファイル共有のサイズ。

**[作成]** を選択して、新しい共有の作成を完了します。 ストレージ アカウントが仮想ネットワーク内にある場合、クライアントも仮想ネットワークに存在しない限り、Azure ファイル共有を正常に作成することはできません。 Azure PowerShell `New-AzRmStorageShare` コマンドレットを使用して、この特定の時点の制限を回避することもできます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) コマンドレットを使用して Azure ファイル共有を作成できます。 次の PowerShell コマンドは、前述の Azure PowerShell を使用したストレージ アカウントの作成セクションで定義されているように、変数 `$resourceGroupName` と `$storageAccountName` が設定されていることを前提としています。 

> [!Important]  
> Premium ファイル共有の場合、`-QuotaGiB` パラメーターでは、ファイル共有のプロビジョニング済みのサイズが参照されます。 ファイル共有のプロビジョニング済みサイズは、使用量に関係なく、課金の対象となる容量です。 Standard ファイル共有は、プロビジョニング済みサイズではなく、使用量に基づいて課金されます。

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

> [!Note]  
> ファイル共有の名前はすべて小文字にする必要があります。 ファイル共有およびファイルへの名前付けの詳細については、「 [Naming and Referencing Shares, Directories, Files, and Metadata (共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照)](https://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI を使用して Azure ファイル共有を作成するには、事前にファイル共有の作成操作を承認するためのストレージ アカウント キーを取得する必要があります。 これは、[`az storage account keys list`](/cli/azure/storage/account/keys) コマンドを使用して行うことができます。

```azurecli-interactive
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

ストレージ アカウント キーを取得したら、[`az storage share create`](/cli/azure/storage/share) コマンドを使用して Azure ファイル共有を作成できます。 

> [!Important]  
> Premium ファイル共有の場合、`--quota` パラメーターでは、ファイル共有のプロビジョニング済みのサイズが参照されます。 ファイル共有のプロビジョニング済みサイズは、使用量に関係なく、課金の対象となる容量です。 Standard ファイル共有は、プロビジョニング済みサイズではなく、使用量に基づいて課金されます。

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

ストレージ アカウントが仮想ネットワーク内に含まれていて、このコマンドを起動しているコンピューターが仮想ネットワークの一部ではない場合、このコマンドは失敗します。 この特定の時点の制限を回避するには、前述の Azure PowerShell `New-AzRmStorageShare` コマンドレットを使用するか、VPN 接続経由など、仮想ネットワークの一部であるコンピューターから Azure CLI を実行します。

---

> [!Note]  
> ファイル共有の名前はすべて小文字にする必要があります。 ファイル共有およびファイルへの名前付けの詳細については、「 [Naming and Referencing Shares, Directories, Files, and Metadata (共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照)](https://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。

### <a name="create-a-hot-or-cool-file-share"></a>ホット ファイル共有またはクールファイル共有を作成する
**General Purpose v2 (GPv2) ストレージ アカウント**のファイル共有には、トランザクション最適化ホット ファイル共有またはクール ファイル共有 (またはそれらの混合) が含まれます。 トランザクション最適化共有は、すべての Azure リージョンで利用できますが、ホット ファイル共有とクール ファイル共有は、[リージョンのサブセット](storage-files-planning.md#storage-tiers)でのみ利用できます。 Azure PowerShell プレビュー モジュールまたは Azure CLI を使用して、ホット ファイル共有またはクール ファイル共有を作成できます。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
Azure portal では、ホット ファイル共有とクール ファイル共有の作成、または既存のトランザクション最適化ファイル共有のホットやクールへの移動は、まだサポートされていません。 PowerShell または Azure CLI でファイル共有を作成するための手順を確認してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myhotshare"

New-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Hot

# You can also change an existing share's tier.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

> [!Note]  
> PowerShell を使用して層を設定し変更する機能が、プレビュー Az.Storage PowerShell モジュールに用意されています。 これらのコマンドレットまたは出力は、一般公開されている Az.Storage PowerShell モジュールでリリースされる前に変更される可能性があるため、この点に留意してスクリプトを作成してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
ファイル共有を作成したり特定の層に移動したりする機能は、最新の Azure CLI 更新プログラムで利用できます。 Azure CLI の更新は、使用しているオペレーティング システムや Linux ディストリビューションに固有です。 システム上で Azure CLI を更新する方法については、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

```bash
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2
# storage accounts. Standard tiers are only available in standard storage accounts.
shareName="myhotshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Hot"
```

> [!Note]  
> `--access-tier` パラメーターで層を設定する機能は、最新の Azure CLI パッケージのプレビューで提供されています。 このコマンドまたはその出力は、一般公開されているものとしてマークされる前に変更される可能性があるため、この点に留意してスクリプトを作成してください。

---

## <a name="next-steps"></a>次のステップ
- [Azure Files のデプロイの計画](storage-files-planning.md)または、[Azure File Sync のデプロイの計画](storage-sync-files-planning.md)。 
- [ネットワークの概要](storage-files-networking-overview.md)。
- ファイル共有を [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md)、[Linux](storage-how-to-use-files-linux.md) に接続してマウントする。