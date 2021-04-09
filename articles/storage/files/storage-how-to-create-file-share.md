---
title: Azure ファイル共有を作成する
titleSuffix: Azure Files
description: Azure portal、PowerShell、Azure CLI を使用して Azure ファイル共有を作成する方法。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 24bee926d84c7a5be3f19c39d39285c2cd486824
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211024"
---
# <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
Azure ファイル共有を作成するには、その使用方法について 3 つの質問に答える必要があります。

- **Azure ファイル共有のパフォーマンス要件はどのようなものですか?**  
    Azure Files には、ハード ディスク ベース (HDD ベース) のハードウェアでホストされる Standard ファイル共有と、ソリッドステート ディスク ベース (SSD ベース) ハードウェアでホストされる Premium ファイル共有が用意されています。

- **Azure ファイル共有の冗長性の要件はどのようなものですか?**  
    Standard ファイル共有では、ローカル冗長 (LRS) ストレージ、ゾーン冗長 (ZRS) ストレージ、geo 冗長 (GRS) ストレージ、または geo ゾーン冗長 (GZRS) ストレージが用意されています。ただし、大きいファイルの共有機能は、ローカル冗長およびゾーン冗長のファイル共有でのみサポートされます。 Premium ファイル共有では、どのような種類の geo 冗長性もサポートされていません。

    Premium ファイル共有は、リージョンのサブセット内でローカル冗長とゾーン冗長で利用できます。 ご自分のリージョンで現在 Premium ファイル共有を使用できるかどうかを見つけるには、Azure の [[リージョン別の利用可能な製品]](https://azure.microsoft.com/global-infrastructure/services/?products=storage) ページを参照してください。 ZRS をサポートするリージョンの詳細については、[Azure Storage の冗長性](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関する記事を参照してください。

- **どのくらいのサイズのファイル共有が必要ですか?**  
    ローカルおよびゾーン冗長ストレージ アカウントでは、Azure ファイル共有は 100 TiB まで拡張できますが、geo および geo ゾーン冗長ストレージ アカウントでは、Azure ファイル共有は 5 TiB までしか拡張できません。 

これら 3 つの選択の詳細については、「[Azure Files のデプロイの計画](storage-files-planning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
- この記事では、既に Azure サブスクリプションが作成されていることを前提としています。 サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
- Azure PowerShell を使用する場合は、[最新バージョンをインストールしてください](/powershell/azure/install-az-ps)。
- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](/cli/azure/install-azure-cli)。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
Azure ファイル共有は、ストレージの共有プールを表す最上位オブジェクトである "*ストレージ アカウント*" にデプロイされます。 このストレージのプールは、複数のファイル共有をデプロイするのに使用できます。 

Azure では、お客様のさまざまなストレージ シナリオに対して複数のストレージ アカウントの種類がサポートされていますが、Azure Files には主に 2 種類のストレージ アカウントがあります。 作成する必要があるストレージ アカウントの種類は、Standard ファイル共有または Premium ファイル共有のどちらを作成するかによって異なります。 

- **General Purpose バージョン 2 (GPv2) ストレージ アカウント**:GPv2 ストレージ アカウントを使うと、Standard のハード ディスク ベース (HDD ベース) のハードウェアに、Azure ファイル共有をデプロイできます。 GPv2 ストレージ アカウントでは、Azure ファイル共有を格納するだけでなく、BLOB コンテナー、キュー、テーブルなどの他のストレージ リソースを格納することもできます。 ファイル共有は、トランザクション最適化 (既定)、ホット、またはクール層にデプロイできます。

- **FileStorage ストレージ アカウント**:FileStorage ストレージ アカウントを使うと、Premium のソリッドステート ディスク ベース (SSD ベース) のハードウェアに、Azure ファイル共有をデプロイできます。 FileStorage アカウントは、Azure ファイル共有を格納するためにのみ使用できます。FileStorage アカウントでその他のストレージ リソース (BLOB コンテナー、キュー、テーブルなど) をデプロイすることはできません。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
Azure portal を使用してストレージ アカウントを作成するには、ダッシュボードから **[+ リソースの作成]** を選択します。 その結果表示される Azure Marketplace の検索ウィンドウで、**ストレージ アカウント** を検索し、検索結果を選択します。 これにより、ストレージ アカウントの概要ページが表示されます。ストレージ アカウントの作成ウィザードを続行するには、 **[作成]** を選択します。

![ブラウザーでのストレージ アカウントの簡易作成オプションのスクリーンショット](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>基本
ストレージ アカウントを作成するために完了する最初のセクションには、 **[基本]** というラベルが付いています。 これには、ストレージ アカウントを作成するために必要なすべてのフィールドが含まれています。 GPv2 ストレージ アカウントを作成するには、確実に **[パフォーマンス]** オプション ボタンを *[Standard]* に設定し、 **[アカウントの種類]** ドロップダウン リストで *[StorageV2 (汎用 v2)]* を選択します。

![[Standard] が選択された [パフォーマンス] オプション ボタンと、[StorageV2] が選択された [アカウントの種類] のスクリーンショット](media/storage-how-to-create-file-share/create-storage-account-1.png)

FileStorage ストレージ アカウントを作成するには、確実に **[パフォーマンス]** オプション ボタンを *[Premium]* に設定し、 **[アカウントの種類]** ドロップダウン リストで *[FileStorage]* を選択します。

![[Premium] が選択された [パフォーマンス] オプション ボタンと、[FileStorage] が選択された [アカウントの種類] のスクリーンショット](media/storage-how-to-create-file-share/create-storage-account-2.png)

その他の基本フィールドは、ストレージ アカウントの選択とは無関係です。
- **ストレージ アカウント名**: 作成するストレージ アカウントリソースの名前。 この名前はグローバルに一意である必要がありますが、任意の名前を使用できます。 SMB 経由で Azure ファイル共有をマウントするときに、ストレージ アカウント名がサーバー名として使用されます。
- **[場所]** :デプロイ先のストレージ アカウントのリージョン。 これは、リソース グループに関連付けられているリージョン、または使用可能なその他のリージョンを指定できます。
- **[Replication]\(レプリケーション\)** :このフィールドはレプリケーションというラベルが付いていますが、実際には **冗長性** を意味します。必要な冗長レベルは、ローカル冗長 (LRS)、ゾーン冗長 (ZRS)、geo 冗長 (GRS)、geo ゾーン冗長 (GZRS) です。 このドロップダウン リストには、Azure ファイル共有には適用されない読み取りアクセス geo 冗長 (RA-GRS) と読み取りアクセス geo ゾーン冗長 (RA-GZRS) も含まれています。これらが選択されたストレージ アカウントで作成されたファイル共有は、実際にはそれぞれ geo 冗長または geo ゾーン冗長のいずれかになります。 

#### <a name="networking"></a>ネットワーク
[ネットワーク] セクションでは、ネットワーク オプションを構成できます。 これらの設定は、ストレージ アカウントを作成する場合は省略可能であり、必要に応じて後で構成できます。 これらのオプションの詳細については、「[Azure Files のネットワークに関する考慮事項](storage-files-networking-overview.md)」を参照してください。

#### <a name="data-protection"></a>データ保護
[データ保護] セクションでは、お使いのストレージ アカウント内の Azure ファイル共有に対する論理的な削除ポリシーを構成できます。 BLOB の論理的な削除、コンテナー、コンテナーのポイントインタイム リストア、バージョン管理、および変更フィードに関連するその他の設定は、Azure BLOB ストレージにのみ適用されます。

#### <a name="advanced"></a>詳細設定
[詳細] セクションには、Azure ファイル共有のいくつかの重要な設定が含まれています。

- **[安全な転送が必須]** :このフィールドは、ストレージ アカウントへの通信でストレージ アカウントが転送中の暗号化を必要とするかどうかを示します。 SMB 2.1 サポートが必要な場合は、これを無効にする必要があります。
- **[大きいファイルの共有]** :このフィールドでは、ストレージ アカウントに対して最大 100 TiB にまたがるファイル共有を有効にします。 この機能を有効にすると、ストレージ アカウントがローカル冗長およびゾーン冗長ストレージのオプションのみに制限されます。 GPv2 ストレージ アカウントを大きいファイルの共有に対して有効にすると、大きいファイルの共有機能を無効にすることはできません。 すべての Premium ファイル共有が 100 TiB までスケールアップできるため、FileStorage ストレージ アカウント (Premium ファイル共有のストレージ アカウント) にはこのオプションはありません。 

![Azure Files に適用される重要な詳細設定のスクリーンショット](media/storage-how-to-create-file-share/create-storage-account-3.png)

[詳細] タブで使用できるその他の設定 (Azure Data Lake Storage Gen 2 の階層型名前空間、既定の BLOB 層、BLOB ストレージ用の NFSv3 など) は、Azure Files には適用されません。

> [!Important]  
> BLOB のアクセス レベルを選択しても、ファイル共有の層には影響しません。

#### <a name="tags"></a>Tags
タグは名前と値のペアで、同じタグを複数のリソースやリソース グループに適用することでリソースを分類したり、統合した請求を表示したりできるようにします。 これらは省略可能であり、ストレージ アカウントの作成後に適用できます。

#### <a name="review--create"></a>確認と作成
ストレージ アカウントを作成する最後の手順は、 **[確認と作成]** タブで **[作成]** ボタンを選択することです。ストレージ アカウントのすべての必須フィールドが入力されていないと、このボタンは使用できません。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell を使用してストレージ アカウントを作成するには、`New-AzStorageAccount` コマンドレットを使用します。 このコマンドレットには多くのオプションがあり、必要なオプションのみが表示されます。 詳細オプションの詳細については、[`New-AzStorageAccount` コマンドレットのドキュメント](/powershell/module/az.storage/new-azstorageaccount)を参照してください。

ストレージ アカウントとそれに続くファイル共有の作成を簡略化するために、いくつかのパラメーターを変数に格納します。 変数の内容は任意の値に置き換えることができますが、ストレージ アカウント名はグローバルに一意である必要があることに注意してください。

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Standard Azure ファイル共有を格納できるストレージ アカウントを作成するには、次のコマンドを使用します。 `-SkuName` パラメーターは、必要な冗長性の種類に関連しています。geo 冗長または geo ゾーン冗長ストレージ アカウントが必要な場合は、`-EnableLargeFileShare` パラメーターも削除する必要があります。

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Premium Azure ファイル共有を格納できるストレージ アカウントを作成するには、次のコマンドを使用します。 `-SkuName` パラメーターが、`Premium` とローカル冗長 (`LRS`) の必要な冗長レベルの両方を含むように変更されていることに注意してください。 `-Kind` パラメーターは `StorageV2` ではなく `FileStorage` です。これは、GPv2 ストレージ アカウントではなく、FileStorage ストレージ アカウントに Premium ファイル共有を作成する必要があるためです。

```powershell
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

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Standard Azure ファイル共有を格納できるストレージ アカウントを作成するには、次のコマンドを使用します。 `--sku` パラメーターは、必要な冗長性の種類に関連しています。geo 冗長または geo ゾーン冗長ストレージ アカウントが必要な場合は、`--enable-large-file-share` パラメーターも削除する必要があります。

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Premium Azure ファイル共有を格納できるストレージ アカウントを作成するには、次のコマンドを使用します。 `--sku` パラメーターが、`Premium` とローカル冗長 (`LRS`) の必要な冗長レベルの両方を含むように変更されていることに注意してください。 `--kind` パラメーターは `StorageV2` ではなく `FileStorage` です。これは、GPv2 ストレージ アカウントではなく、FileStorage ストレージ アカウントに Premium ファイル共有を作成する必要があるためです。

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>ファイル共有の作成
ストレージ アカウントを作成したら、後はファイル共有を作成するだけです。 このプロセスは、Premium ファイル共有と Standard ファイル共有のどちらを使用しているかに関係なく、ほぼ同じです。 次の相違点について検討してください。

Standard ファイル共有は、Standard の層であるトランザクション最適化 (既定)、ホット、クールのいずれかにデプロイできます。 これはストレージ アカウントのファイル共有層ごとのもので、**BLOB のアクセス レベル** の影響を受けません (このプロパティは Azure Blob Storage にのみ関連します。Azure Files には関係ありません)。 共有の層は、デプロイ後いつでも変更できます。 Premium ファイル共有を Standard の層に直接変換することはできません。

> [!Important]  
> ファイル共有は、GPv2 ストレージ アカウントの種類内の層 (トランザクション最適化、ホット、クール) をまたいで移動できます。 層の間で共有を移動させると、トランザクションが発生します。ホットな層からクールな層に移動すると、共有内の各ファイルについてクールな層の書き込みトランザクション料金が発生します。一方、クールな層からホットな層に移動すると、共有内の各ファイルについて、クールな層の読み取りトランザクション料金が発生します。

**クォータ** プロパティの意味は、Premium と Standard のファイル共有の間で若干異なります。

- Standard ファイル共有の場合、それはエンド ユーザーがアクセスできない Azure ファイル共有の上限です。 クォータが指定されていない場合、Standard ファイル共有は最大 100 TiB (ストレージ アカウントに対して大きいファイルの共有プロパティが設定されていない場合は 5 TiB) にまたがることができます。

- Premium ファイル共有の場合、クォータは **プロビジョニング済みのサイズ** を意味します。 実際の使用量に関係なく、プロビジョニング済みのサイズが課金される容量です。 Premium ファイル共有を計画する方法の詳細については、[Premium ファイル共有のプロビジョニング](understanding-billing.md#provisioned-model)に関するセクションを参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
ストレージ アカウントを作成したばかりの場合は、[デプロイ] 画面から **[リソースに移動]** を選択して移動できます。 ストレージ アカウントで、 **[ファイル共有]** というラベルの付いたタイルを選択します (ストレージ アカウントの目次を介して **ファイル共有** に移動することもできます)。

![[ファイル共有] タイルのスクリーンショット](media/storage-how-to-create-file-share/create-file-share-1.png)

ファイル共有リストには、このストレージ アカウントで以前に作成したファイル共有がすべて表示されます。ファイル共有がまだ作成されていない場合は、空のテーブルが表示されます。 **[+ ファイル共有]** を選択して、新しいファイル共有を作成します。

新しい [ファイル共有] ブレードが画面に表示されます。 新しいファイル共有ブレードのフィールドをすべて入力して、ファイル共有を作成します。

- **[名前]** : 作成するファイルの名前。
- **[クォータ]** : Standard ファイル共有のファイル共有のクォータ。Premium ファイル共有のプロビジョニング済みのファイル共有のサイズ。
- **[層]** : ファイル共有用に選択した層。 このフィールドは、**汎用 (GPv2) ストレージ アカウント** でのみ使用できます。 トランザクション最適化、ホット、またはクールを選択できます。 共有の層はいつでも変更できます。 移行中は可能な限り最もホットな層を選択してトランザクションの費用を最小限に抑え、移行の完了後に必要に応じて下位の層に切り替えることをお勧めします。

**[作成]** を選択して、新しい共有の作成を完了します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) コマンドレットを使用して Azure ファイル共有を作成できます。 次の PowerShell コマンドは、前述の Azure PowerShell を使用したストレージ アカウントの作成セクションで定義されているように、変数 `$resourceGroupName` と `$storageAccountName` が設定されていることを前提としています。 

次の例では、`-AccessTier` パラメーターを使用して、明示的な層でファイル共有を作成します。 そのためには、例に示されているように、プレビューの Az.Storage モジュールを使用する必要があります。 GA の Az.Storage モジュールを使用しているため、または、このコマンドを含めていないために、層が指定されていない場合、Standard ファイル共有の既定の層はトランザクション最適化です。

> [!Important]  
> Premium ファイル共有の場合、`-QuotaGiB` パラメーターでは、ファイル共有のプロビジョニング済みのサイズが参照されます。 ファイル共有のプロビジョニング済みサイズは、使用量に関係なく、課金の対象となる容量です。 Standard ファイル共有は、プロビジョニング済みサイズではなく、使用量に基づいて課金されます。

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) コマンドを使用して Azure ファイル共有を作成できます。 次の Azure CLI コマンドは、前述の Azure CLI を使用したストレージ アカウント作成のセクションで定義されているように、変数 `$resourceGroupName` と `$storageAccountName` が設定されていることを前提としています。

> [!Important]  
> Premium ファイル共有の場合、`--quota` パラメーターでは、ファイル共有のプロビジョニング済みのサイズが参照されます。 ファイル共有のプロビジョニング済みサイズは、使用量に関係なく、課金の対象となる容量です。 Standard ファイル共有は、プロビジョニング済みサイズではなく、使用量に基づいて課金されます。

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> ファイル共有の名前はすべて小文字にする必要があります。 ファイル共有とファイルの名前付けの詳細については、「[Naming and referencing shares, directories, files, and metadata (共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照)](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)」を参照してください。

### <a name="changing-the-tier-of-an-azure-file-share"></a>Azure ファイル共有の層の変更
**汎用 v2 (GPv2) ストレージ アカウント** にデプロイされるファイル共有は、トランザクション最適化、ホット、クールのいずれかの層に格納できます。 Azure ファイル共有の層はいつでも変更でき、前述のトランザクション コストが適用されます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
ストレージ アカウントのメイン ページで、 **[ファイル共有]** を選択し、 **[ファイル共有]** というラベルの付いたタイルを選択します (ストレージ アカウントの目次を介して **[ファイル共有]** に移動することもできます)。

![[ファイル共有] タイルのスクリーンショット](media/storage-how-to-create-file-share/create-file-share-1.png)

ファイル共有のテーブルの一覧で、層を変更するファイル共有を選択します。 ファイル共有の概要ページで、メニューから **[層の変更]** を選択します。

![[層の変更] ボタンが強調表示されているファイル共有の概要ページのスクリーンショット](media/storage-how-to-create-file-share/change-tier-0.png)

表示されたダイアログで、目的の層 (トランザクション最適化、ホット、またはクール) を選択します。

![層の変更ダイアログのスクリーンショット](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
次の PowerShell コマンドレットは、このドキュメントのこれまでのセクションで説明したように、`$resourceGroupName`、`$storageAccountName`、`$shareName` 変数が設定されていることを前提としています。

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
次の Azure CLI コマンドは、このドキュメントのこれまでのセクションで説明したように、`$resourceGroupName`、`$storageAccountName`、`$shareName` 変数が設定されていることを前提としています。

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>次のステップ
- [Azure Files のデプロイの計画](storage-files-planning.md)または、[Azure File Sync のデプロイの計画](storage-sync-files-planning.md)。 
- [ネットワークの概要](storage-files-networking-overview.md)。
- ファイル共有を [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md)、[Linux](storage-how-to-use-files-linux.md) に接続してマウントする。