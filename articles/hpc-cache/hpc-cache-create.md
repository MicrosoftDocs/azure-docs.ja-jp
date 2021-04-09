---
title: Azure HPC キャッシュを作成する
description: Azure HPC Cache インスタンスを作成する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 63a179f7123f088c9c60fbfb8996a5b21d1c704d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773268"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC キャッシュを作成する

Azure portal または Azure CLI を使用してキャッシュを作成します。

![Azure portal に表示されるキャッシュの概要と下部の作成ボタンのスクリーンショット](media/hpc-cache-home-page.png)

キャッシュの作成とストレージ ターゲットの追加に関する[ビデオ デモ](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)を視聴するには、次の画像をクリックしてください。

[![ビデオのサムネイル:Azure HPC Cache:セットアップ (クリックしてビデオ ページにアクセス)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[ポータル](#tab/azure-portal)

## <a name="define-basic-details"></a>基本的な詳細を定義する

![Azure portal のプロジェクト詳細ページのスクリーンショット](media/hpc-cache-create-basics.png)

**[プロジェクトの詳細]** で、キャッシュのホストとなるサブスクリプションとリソース グループを選択します。

**[サービスの詳細]** で、キャッシュの名前と、これらのその他の属性を設定します。

* 場所 - [サポートされているリージョン](hpc-cache-overview.md#region-availability)のいずれかを選択します。
* 仮想ネットワーク - 既存のものを選択するか、新しい仮想ネットワークを作成することができます。
* サブネット - 少なくとも 64 個の IP アドレス (/24) を持つサブネットを選択または作成します。 このサブネットは、この Azure HPC Cache インスタンスにのみ使用する必要があります。

## <a name="set-cache-capacity"></a>キャッシュ容量を設定する
<!-- referenced from GUI - update aka.ms link if you change this header text -->

**[キャッシュ]** ページで、キャッシュの容量を設定する必要があります。 ご自分のキャッシュで保持できるデータの量とクライアント要求の処理の迅速さは、ここで設定する値によって決まります。

また、容量はキャッシュのコストにも影響します。

これらの 2 つの値を設定して容量を選択してください。

* キャッシュの最大データ転送速度 (スループット) (GB/秒)
* キャッシュ データ用に割り当てるストレージの容量 (TB)

選択可能ないずれかのスループット値とキャッシュ ストレージ サイズを選択してください。

実際のデータ転送速度は、ワークロードとネットワーク速度、ストレージ ターゲットの種類によって異なることに注意してください。 選択した値により、キャッシュ システム全体の最大スループットが設定されますが、その一部はオーバーヘッド タスクに使用されます。 たとえば、キャッシュに既に格納されていないファイルがクライアントによって要求された場合、またはファイルが古いとマークされている場合、キャッシュでは、スループットの一部を使用して、バックエンド ストレージからファイルがフェッチされます。

Azure HPC Cache では、キャッシュ ヒット率を最大限に高めるために、どのファイルをキャッシュして事前に読み込むかが管理されます。 キャッシュの内容は絶えず評価され、アクセスされる頻度が低くなったファイルは長期ストレージに移されます。 メタデータ用の追加領域やその他のオーバーヘッドと、アクティブな作業ファイル一式を無理なく保持できるキャッシュ ストレージ サイズを選んでください。

![キャッシュ サイズ設定ページのスクリーンショット](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Azure Key Vault の暗号化を有効にする (省略可能)

**[Disk encryption keys]\(ディスク暗号化キー\)** ページは **[キャッシュ]** タブと **[タグ]** タブの間に表示されます。<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

キャッシュ ストレージで使用する暗号化キーを管理する場合は、 **[Disk encryption keys] (ディスク暗号化キー)** ページで、お使いの Azure Key Vault の情報を入力します。 キー コンテナーは、キャッシュと同じリージョンおよび同じサブスクリプションに存在する必要があります。

カスタマー マネージド キーが不要な場合は、このセクションを省略できます。 Azure では、既定で Microsoft のマネージド キーを使用してデータを暗号化します。 詳細については、[Azure Storage の暗号化](../storage/common/storage-service-encryption.md)に関する記事を参照してください。

> [!NOTE]
>
> * キャッシュの作成後に、Microsoft のマネージド キーとカスタマー マネージド キーを切り換えることはできません。
> * キャッシュが作成されたら、それに対してキー コンテナーへのアクセスを承認する必要があります。 キャッシュの **[概要]** ページの **[暗号化を有効にする]** ボタンをクリックして、暗号化を有効にします。 この手順は、キャッシュの作成から 90 分以内に実行してください。
> * キャッシュ ディスクは、この承認の後に作成されます。 これは、最初のキャッシュ作成時間は短いが、アクセスを承認してから 10 分以上はキャッシュを使用できないことを意味します。

カスタマー マネージド キー暗号化プロセスの詳細については、「[Azure HPC Cache にカスタマー マネージド暗号化キーを使用する](customer-keys.md)」を参照してください。

![[Customer managed]\(カスタマー マネージド\) が選択され、キー コンテナーのフィールドが表示されている暗号化キー ページのスクリーンショット](media/create-encryption.png)

カスタマー マネージド キー暗号化を選択するには、 **[Customer managed]\(カスタマー マネージド\)** を選択します。 キー コンテナー指定のフィールドが表示されます。 使用する Azure Key Vault を選択し、このキャッシュに使用するキーとバージョンを選択します。 キーは、2048 ビット RSA キーである必要があります。 このページで、新しいキー コンテナー、キー、キー バージョンを作成できます。

キャッシュを作成したら、それに対してキー コンテナー サービスの使用を承認する必要があります。 詳細については、「[キャッシュから Azure Key Vault の暗号化を承認する](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)」を参照してください。

## <a name="add-resource-tags-optional"></a>リソース タグを追加する (省略可)

**[タグ]** ページでは、お使いの Azure HPC キャッシュ インスタンスに [リソース タグ](../azure-resource-manager/management/tag-resources.md)を追加できます。

## <a name="finish-creating-the-cache"></a>キャッシュの作成を完了する

新しいキャッシュを構成したら、 **[確認と作成]** タブをクリックします。選択内容がポータルによって検証されるほか、自分で選択内容を確認することができます。 すべて正しければ **[作成]** をクリックしてください。

キャッシュの作成には 10 分程度かかります。 進行状況は、Azure portal の通知パネルで追跡できます。

![ポータルのキャッシュ作成画面 ("デプロイが進行中です" ページと "通知" ページ) のスクリーンショット](media/hpc-cache-deploy-status.png)

作成が完了すると、新しい Azure HPC Cache インスタンスへのリンクと共に通知が表示され、ご利用のサブスクリプションの **[リソース]** リストにキャッシュが表示されます。

![Azure portal における Azure HPC Cache インスタンスのスクリーンショット](media/hpc-cache-new-overview.png)

> [!NOTE]
> キャッシュでカスタマー マネージド暗号化キーを使用する場合は、デプロイの状態が完了に変更される前に、リソースの一覧にそのキャッシュが表示されることがあります。 キャッシュの状態が **[Waiting for key]\(キーの待機中\)** になるとすぐに、それに対してキー コンテナーの使用を [承認する](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)ことができます。

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Azure CLI でキャッシュを作成する

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

> [!NOTE]
> Azure CLI では、現在のところ、カスタマー マネージド暗号化キーを使用したキャッシュの作成はサポートされていません。 Azure portal を使用します。

[az hpc-cache create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) コマンドを使用して、新しい Azure HPC Cache を作成します。

以下の値を指定します。

* キャッシュのリソース グループ名
* キャッシュ名
* Azure リージョン
* キャッシュのサブネット (次の形式):

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  キャッシュのサブネットには少なくとも 64 個の IP アドレス (/24) が必要で、そこに他のリソースを格納することはできません。

* キャッシュ容量。 2 つの値で、Azure HPC Cache の最大スループットを設定します。

  * キャッシュ サイズ (GB)
  * キャッシュ インフラストラクチャで使用される仮想マシンの SKU

  [az hpc-cache skus list](/cli/azure/ext/hpc-cache/hpc-cache/skus) を実行すると、使用可能な SKU と、各 SKU で有効なキャッシュ サイズ オプションが表示されます。 キャッシュ サイズ オプションの範囲は 3 TB ～ 48 TB ですが、サポートされるのは一部の値のみです。

  次の表は、このドキュメントの準備時点 (2020 年 7 月) で有効な、キャッシュ サイズと SKU の組み合わせを示しています。

  | キャッシュ サイズ | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3,072 GB    | 可         | no          | no          |
  | 6144 GB    | はい         | はい         | no          |
  | 12288 GB   | 可         | はい         | 可         |
  | 24576 GB   | Ｘ          | はい         | 可         |
  | 49152 GB   | Ｘ          | no          | 可         |

  料金、スループット、およびワークフローに応じてキャッシュのサイズを適切に設定する方法については、ポータルの指示タブにある「**キャッシュ容量を設定する**」セクションを参照してください。

キャッシュ作成の例:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

キャッシュの作成には数分かかります。 成功すると、create コマンドから次のような出力が返されます。

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

メッセージには、以下の項目を含む便利な情報がいくつか含まれています。

* クライアントのマウント アドレス - クライアントをキャッシュに接続する準備ができたら、これらの IP アドレスを使用します。 詳細については、「[Azure HPC Cache をマウントする](hpc-cache-mount.md)」を参照してください。
* アップグレードの状態 - ソフトウェア更新プログラムがリリースされると、このメッセージが変化します。 [キャッシュ ソフトウェアのアップグレード](hpc-cache-manage.md#upgrade-cache-software)は、都合のよいときに手動で行えます。または、数日後には自動的に適用されます。

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Az.HPCCache PowerShell モジュールは、現在パブリック プレビュー段階にあります。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 運用環境のワークロードにはお勧めしません。 一部の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="requirements"></a>必要条件

ローカルで PowerShell を使用する場合は、Az PowerShell モジュールをインストールしたうえで、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して自分の Azure アカウントに接続する必要があります。 Az PowerShell モジュールのインストールの詳細については、「[Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。 Cloud Shell の使用を選択した場合、詳細については「[Azure Cloud Shell の概要](../cloud-shell/overview.md)」を参照してください。

> [!IMPORTANT]
> **Az.HPCCache** PowerShell モジュールがプレビュー段階にある間は、`Install-Module` コマンドレットを使用して、これを個別にインストールする必要があります。 この PowerShell モジュールは、一般提供されると、将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Azure PowerShell でキャッシュを作成する

> [!NOTE]
> Azure PowerShell では、現在のところ、カスタマー マネージド暗号化キーを使用したキャッシュの作成はサポートされていません。 Azure portal を使用します。

新しい Azure HPC Cache を作成するには、[New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) コマンドレットを使用します。

次の値を指定します。

* キャッシュのリソース グループ名
* キャッシュ名
* Azure リージョン
* キャッシュのサブネット (次の形式):

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  キャッシュのサブネットには少なくとも 64 個の IP アドレス (/24) が必要で、そこに他のリソースを格納することはできません。

* キャッシュ容量。 2 つの値で、Azure HPC Cache の最大スループットを設定します。

  * キャッシュ サイズ (GB)
  * キャッシュ インフラストラクチャで使用される仮想マシンの SKU

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) を実行すると、使用可能な SKU と、それぞれの有効なキャッシュ サイズ オプションが表示されます。 キャッシュ サイズ オプションの範囲は 3 TB ～ 48 TB ですが、サポートされるのは一部の値のみです。

  次の表は、このドキュメントの準備時点 (2020 年 7 月) で有効な、キャッシュ サイズと SKU の組み合わせを示しています。

  | キャッシュ サイズ | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3,072 GB    | 可         | no          | no          |
  | 6144 GB    | はい         | はい         | no          |
  | 12,288 GB   | はい         | はい         | はい         |
  | 24,576 GB   | no          | はい         | はい         |
  | 49,152 GB   | no          | no          | 可         |

  料金、スループット、およびワークフローに応じてキャッシュのサイズを適切に設定する方法については、ポータルの指示タブにある「**キャッシュ容量を設定する**」セクションを参照してください。

キャッシュ作成の例:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

キャッシュの作成には数分かかります。 成功すると、create コマンドから次の出力が返されます。

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

メッセージには、以下の項目を含む便利な情報がいくつか含まれています。

* クライアントのマウント アドレス - クライアントをキャッシュに接続する準備ができたら、これらの IP アドレスを使用します。 詳細については、「[Azure HPC Cache をマウントする](hpc-cache-mount.md)」を参照してください。
* アップグレードの状態 - ソフトウェア更新プログラムがリリースされると、このメッセージが変化します。 [キャッシュ ソフトウェアのアップグレード](hpc-cache-manage.md#upgrade-cache-software)は、都合のよいときに手動で行えます。または、数日後には自動的に適用されます。

---

## <a name="next-steps"></a>次のステップ

**[リソース]** の一覧にキャッシュが表示されたら、次の手順に進むことができます。

* [ストレージ ターゲットを定義](hpc-cache-add-storage.md)して、キャッシュがデータ ソースにアクセスできるようにします。
* カスタマー マネージド暗号化キーを使用する場合は、キャッシュの概要ページで [Azure Key Vault の暗号化を承認](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache)して、キャッシュのセットアップを完了する必要があります。 この手順は、ストレージを追加する前に実行する必要があります。 詳細については、[カスタマー マネージド暗号化キーの使用](customer-keys.md)に関する記事を参照してください。
