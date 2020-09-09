---
title: Azure HPC Cache にストレージを追加する
description: Azure HPC Cache で長期的なファイルの保管にオンプレミス NFS システムまたは Azure BLOB コンテナーを使用できるようにストレージ ターゲットを定義する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 585ea3b5ddd16acb9af83c1c1e0e4aa6ca9e631a
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826706"
---
# <a name="add-storage-targets"></a>ストレージ ターゲットを追加する

"*ストレージ ターゲット*" は、Azure HPC Cache を通じてアクセスされるファイルのバックエンド ストレージです。 NFS ストレージ (オンプレミスのハードウェア システムなど) を追加できるほか、Azure BLOB にデータを格納することができます。

1 つのキャッシュに対して最大 10 個の異なるストレージ ターゲットを定義できます。 このキャッシュでは、すべてのストレージ ターゲットが 1 つの集約された名前空間で提供されます。

キャッシュの仮想ネットワークからストレージのエクスポートにアクセスできなければならないことに注意してください。 オンプレミスのハードウェア ストレージの場合、NFS ストレージへのアクセスに使用されるホスト名を解決できる DNS サーバーの設定が必要になることがあります。 詳細については、「[DNS アクセス](hpc-cache-prerequisites.md#dns-access)」を参照してください。

キャッシュを作成した後で、ストレージ ターゲットを追加します。 この手順は、追加するのが Azure Blob Storage であるか NFS エクスポートであるかによって若干異なります。 それぞれの詳細を以下に示します。

Azure portal からのキャッシュの作成とストレージ ターゲットの追加に関する[ビデオ デモ](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)を視聴するには、次の画像をクリックしてください。

[![ビデオのサムネイル:Azure HPC Cache: セットアップ (クリックしてビデオ ページにアクセス)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="view-storage-targets"></a>ストレージ ターゲットを表示する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal からキャッシュ インスタンスを開き、左側のサイド バーにある **[ストレージ ターゲット]** をクリックします。 ストレージ ターゲットのページには、既存のターゲットがすべて表示されるほか、新たに追加するためのリンクが表示されます。

![サイドバーの [ストレージ ターゲット] リンクのスクリーンショット。[設定] と [監視] というカテゴリ見出しの間にある [構成] という見出しの下にあります](media/hpc-cache-storage-targets-sidebar.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[az hpc-cache storage-target list](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) オプションを使用すると、キャッシュ用の既存のストレージ ターゲットを表示することができます。 キャッシュ名とリソース グループを指定します (それがグローバルに設定している場合は除きます)。

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

特定のストレージ ターゲットに関する詳細を表示するには、[az hpc-cache storage-target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) を使用します (ストレージ ターゲットを名前で指定します)。

例:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="add-a-new-azure-blob-storage-target"></a>新しい Azure Blob Storage ターゲットを追加する

新しい Blob Storage ターゲットには、空の BLOB コンテナーか、Azure HPC Cache クラウド ファイル システム フォーマットでデータが事前設定されているコンテナーが必要です。 BLOB コンテナーの事前読み込みの詳細については、[Azure Blob Storage へのデータの移動](hpc-cache-ingest.md)に関するページを参照してください。

Azure portal の **[ストレージ ターゲットの追加]** ページには、追加する直前に新しい BLOB コンテナーを作成するオプションが含まれています。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

![新しい Azure Blob Storage ターゲットの情報が事前設定されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/hpc-cache-add-blob.png)

Azure BLOB コンテナーを定義するには、次の情報を入力します。

* **[ストレージ ターゲット名]** - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。
* **[ターゲットの種類]** - **[BLOB]** を選択します。
* **[ストレージ アカウント]** - 使用するアカウントを選択します。

  [アクセス ロールの追加](#add-the-access-control-roles-to-your-account)に関するセクションの説明に従って、ストレージ アカウントへのアクセスをキャッシュ インスタンスに承認する必要があります。

  使用できるストレージ アカウントの種類の詳細については、「[Blob Storage の要件](hpc-cache-prerequisites.md#blob-storage-requirements)」を参照してください。

* **[ストレージ コンテナー]** - このターゲットの BLOB コンテナーを選択するか、 **[新規作成]** をクリックします。

  ![新しいコンテナーの名前とアクセス レベル (プライベート) を指定するダイアログのスクリーンショット](media/add-blob-new-container.png)

* **[Virtual namespace path]\(仮想名前空間パス\)** - このストレージ ターゲットに使用するクライアント側のファイル パスを設定します。 仮想名前空間の機能の詳細については、「[集約された名前空間を構成する](hpc-cache-namespace.md)」を参照してください。

完了したら、 **[OK]** をクリックしてストレージ ターゲットを追加します。

> [!NOTE]
> お使いのストレージ アカウント ファイアウォールが "選択されているネットワーク" のみにアクセスできるように制限されている場合、「[BLOB ストレージ アカウントのファイアウォール設定を回避する](hpc-cache-blob-firewall-fix.md)」に記されている一時的な回避策をご利用ください。

### <a name="add-the-access-control-roles-to-your-account"></a>アクセス制御ロールをアカウントに追加する

Azure HPC Cache では、Azure Blob Storage ターゲットのストレージ アカウントにキャッシュ サービスがアクセスすることを承認する場合、[Azure ロールベースのアクセス制御 (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) が使用されます。

ストレージ アカウント所有者は、"HPC Cache リソース プロバイダー" ユーザーの[ストレージ アカウント共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)ロールと[ストレージ BLOB データ共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)ロールを明示的に追加する必要があります。

この作業は事前に行えるほか、Blob Storage ターゲットを追加するページ上のリンクをクリックして行うこともできます。 ロールの設定を Azure 環境経由で反映させるとき、最大 5 分かかる場合があることにご留意ください。ロールの追加後、少し待ってからストレージ ターゲットを作成してください。

Azure ロールを追加する手順:

1. ストレージ アカウントの **[アクセス制御 (IAM)]** ページを開きます ( **[ストレージ ターゲットの追加]** ページのリンクをクリックすると自動的に、選択したアカウントについて、このページが表示されます)。

1. ページ上部の **+** をクリックして **[ロールの割り当てを追加する]** を選択します。

1. [ストレージ アカウント共同作成者] ロールを一覧から選択します。

1. **[アクセスの割り当て先]** フィールドでは、既定値を選択したままにしてください ("Azure AD のユーザー、グループ、サービス プリンシパル")。  

1. **[選択]** フィールドで、"hpc" を検索します。  この文字列は "HPC Cache リソース プロバイダー" という名前の 1 つのセキュリティ プリンシパルと一致するはずです。 そのプリンシパルをクリックして選択します。

   > [!NOTE]
   > "hpc" の検索が機能しない場合は、代わりに文字列 "storagecache" を使用してみてください。 (一般提供開始前に) プレビューに参加したユーザーは、サービス プリンシパルに以前の名前を使用しなければならない可能性があります。

1. 下部にある **[保存]** をクリックします。

1. このプロセスを繰り返して、"Storage Blob Data Contributor" というロールを割り当てます。  

![ロールの割り当ての追加の GUI のスクリーンショット](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

### <a name="prerequisite-storage-account-access"></a>前提条件:ストレージ アカウント アクセス

BLOB ストレージ ターゲットを追加する前に、ストレージ アカウントにアクセスするための適切なロールをキャッシュが備えていること、ファイアウォール設定によってストレージターゲットの作成が許可されることを確認してください。

Azure HPC Cache では、Azure Blob Storage ターゲットのストレージ アカウントにキャッシュ サービスがアクセスすることを承認する場合、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/index.yml) が使用されます。

ストレージ アカウント所有者は、"HPC Cache リソース プロバイダー" ユーザーの[ストレージ アカウント共同作成者](../role-based-access-control/built-in-roles.md#storage-account-contributor)ロールと[ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)ロールを明示的に追加する必要があります。

キャッシュにこれらのロールがない場合、ストレージ ターゲットの作成は失敗します。

ロールの設定を Azure 環境経由で反映させるとき、最大 5 分かかる場合があります。そのため、ロールの追加後、少し待ってからストレージ ターゲットを作成してください。

詳細な手順については、[Azure CLI を使用して Azure でのロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-cli.md)方法に関するページを参照してください。

また、ストレージ アカウントのファイアウォール設定も確認してください。 アクセスを "選択されたネットワーク" のみに制限するようにファイアウォールが設定されている場合、ストレージ ターゲットの作成は失敗する可能性があります。 「[BLOB ストレージ アカウントのファイアウォール設定を回避する](hpc-cache-blob-firewall-fix.md)」に記載されている回避策を使用してください。

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Azure CLI を使用して BLOB ストレージ ターゲットを追加する

Azure Blob ストレージ ターゲットを定義するには、[az hpc-cache blob-storage-target add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) インターフェイスを使用します。

標準のリソース グループおよびキャッシュ名のパラメーターに加えて、ストレージ ターゲットに対して次のオプションを指定する必要があります。

* ``--name`` - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。

* ``--storage-account`` - アカウント識別子。次の形式になります: /subscriptions/ *<subscription_id>* /resourceGroups/ *<storage_resource_group>* /providers/Microsoft.Storage/storageAccounts/ *<account_name>*

  使用できるストレージ アカウントの種類の詳細については、「[Blob Storage の要件](hpc-cache-prerequisites.md#blob-storage-requirements)」を参照してください。

* ``--container-name`` - このストレージ ターゲットに使用するコンテナーの名前を指定します。

* ``--virtual-namespace-path`` - このストレージ ターゲットに使用するクライアント側のファイル パスを設定します。 パスを引用符で囲みます。 仮想名前空間の機能の詳細については、「[集約された名前空間を計画する](hpc-cache-namespace.md)」を参照してください。

コマンドの例:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>新しい NFS ストレージ ターゲットを追加する

NFS ストレージ ターゲットには、BLOB ストレージ ターゲットよりも多くのフィールドがあります。 これらのフィールドでは、ストレージ エクスポートへの到達方法やそのデータを効率的にキャッシュする方法を指定します。 また、NFS ストレージ ターゲットでは、利用できるエクスポートが NFS ホストに複数ある場合、複数の名前空間パスを作成できます。

![NFS ターゲットが定義されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/add-nfs-target.png)

> [!NOTE]
> NFS ストレージ ターゲットを作成する前に、ストレージ システムが Azure HPC Cache からアクセス可能であり、アクセス許可要件を満たしていることを確認します。 キャッシュからストレージ システムにアクセスできない場合、ストレージ ターゲットの作成は失敗します。 詳細については、「[NFS ストレージの要件](hpc-cache-prerequisites.md#nfs-storage-requirements)」と「[NAS 構成および NFS ストレージ ターゲットに関する問題のトラブルシューティング](troubleshoot-nas.md)」を参照してください。

### <a name="choose-a-usage-model"></a>使用モデルを選択する
<!-- referenced from GUI - update aka.ms link if you change this heading -->

NFS ストレージ システムを指すストレージ ターゲットを作成するときは、そのターゲットの使用モデルを選択する必要があります。 どのようにデータがキャッシュされるかは、このモデルによって決まります。

次の 3 つのオプションがあります。

* **負荷が高く、頻度の低い書き込みを読み取ります** - 静的、またはほとんど変更されないファイルへの読み取りアクセスを速くする場合にこのオプションを使用します。

  このオプションを選択すると、クライアントにより読み取られるファイルがキャッシュされますが、書き込みは直後にバックエンド ストレージに渡されます。 キャッシュに格納されているファイルが NFS ストレージ ボリューム上のファイルと比較されることはありません。

  最初にキャッシュに書き込まず、ストレージ システムで直接、ファイルが変更されるリスクがある場合、このオプションは使用しないでください。 直接変更されてしまうと、キャッシュされたバージョンのファイルはバック エンドからの変更で更新されず、データ セットに整合性がなくなります。

* **書き込みが 15% を超えています** - このオプションを使用すると、書き込みと読み取りの両方が速くなります。 このオプションを使用するとき、クライアントでは、バックエンド ストレージを直接マウントするのではなく、Azure HPC Cache 経由でファイルにアクセスする必要があります。 キャッシュされたファイルでは、バック エンドで格納されていない最近の変更が反映されます。

  この使用モデルでは、キャッシュのファイルがバックエンド ストレージのファイルに対して照合されることはありません。 キャッシュされたバージョンのファイルの方が新しいと見なされます。 キャッシュに存在する変更後のファイルは、追加の変更なしでキャッシュに 1 時間留まった後にバックエンド ストレージ システムに書き込まれます。

* **クライアントは、キャッシュをバイパスして NFS ターゲットに書き込みます** - ワークフローのクライアントで、先にキャッシュに書き込まず、ストレージ システムにデータが直接書き込まれる場合にこのオプションを選択します。 クライアントによって要求されたファイルがキャッシュされますが、クライアントからのそれらのファイルの変更は、直後にバックエンド ストレージ システムに戻されます。

  この使用モデルでは、キャッシュに存在するファイルの更新がないか、バックエンド バージョンが頻繁に確認されます。 この検証によって、キャッシュの外でファイルを変更することが可能になり、同時にデータの整合性が維持されます。

このテーブルは、使用モデルの違いをまとめたものです。

| 使用モデル                   | キャッシュ モード | バックエンド検証 | ライトバックの最大遅延 |
|-------------------------------|--------------|-----------------------|--------------------------|
| 負荷が高く、頻度の低い書き込みを読み取ります | Read         | なし                 | なし                     |
| 書き込みが 15% を超えています       | 読み取り/書き込み   | なし                 | 1 時間                   |
| クライアントはキャッシュをバイパス      | Read         | 30 秒            | なし                     |

### <a name="create-an-nfs-storage-target"></a>NFS ストレージ ターゲットを作成する

### <a name="portal"></a>[ポータル](#tab/azure-portal)

![NFS ターゲットが定義されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/add-nfs-target.png)

NFS を使用したストレージ ターゲットについて次の情報を入力します。

* **[ストレージ ターゲット名]** - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。

* **[ターゲットの種類]** - **[NFS]** を選択します。

* **[ホスト名]** - NFS ストレージ システムの IP アドレスまたは完全修飾ドメイン名を入力します (ドメイン名を使用するのは、名前を解決できる DNS サーバーにキャッシュからアクセスできる場合のみです)。

* **[使用モデル]** - ワークフローに基づいていずれかのデータ キャッシュ プロファイルを選択します ([前述の「使用モデルを選択する」](#choose-a-usage-model)を参照)。

### <a name="nfs-namespace-paths"></a>NFS 名前空間パス

各パスが同じストレージ システム上の別のエクスポートまたはサブディレクトリを表している限り、1 つの NFS ストレージ ターゲットに複数の仮想パスを使用できます。

1 つのストレージ ターゲットからすべてのパスを作成します。

ストレージ ターゲットではいつでも、[名前空間パスを追加したり、編集したり](hpc-cache-edit-storage.md)できます。

各名前空間パスに対して、次の値を入力します。

* **[Virtual namespace path]\(仮想名前空間パス\)** - このストレージ ターゲットに使用するクライアント側のファイル パスを設定します。 仮想名前空間の機能の詳細については、「[集約された名前空間を構成する](hpc-cache-namespace.md)」を参照してください。

* **[NFS export path]\(NFS エクスポート パス\)** - NFS エクスポートのパスを入力します。

* **[Subdirectory path]\(サブディレクトリ パス\)** - エクスポートの特定のサブディレクトリをマウントしたい場合は、ここに入力します。 それ以外の場合、このフィールドは空白のままにしてください。

完了したら、 **[OK]** をクリックしてストレージ ターゲットを追加します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

ストレージ ターゲットを作成するには、Azure CLI コマンド [az hpc-cache nfs-storage-target add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) を使用します。 キャッシュ名とキャッシュ リソース グループに加えて、次の値を指定します。

* ``--name`` - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。
* ``--nfs3-target`` - NFS ストレージ システムの IP アドレス (名前を解決できる DNS サーバーへのアクセス権がキャッシュにある場合、ここでは完全修飾ドメイン名を使用できます)。
* ``--nfs3-usage-model`` - データ キャッシュ プロファイルの 1 つ (前述の「[使用モデルを選択する](#choose-a-usage-model)」を参照)。

  使用モデルの名前を確認するには、コマンド [az hpc-cache usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list) を使用します。

* ``--junction`` - junction パラメーターによって、クライアント側の仮想ファイル パスがストレージ システム上のエクスポート パスにリンクされます。

  各パスが同じストレージ システム上の別のエクスポートまたはサブディレクトリを表している限り、1 つの NFS ストレージ ターゲットに複数の仮想パスを使用できます。 1 つのストレージ ターゲット上の 1 つのストレージ システムに対するすべてのパスを作成します。

  ストレージ ターゲットではいつでも、[名前空間パスを追加したり、編集したり](hpc-cache-edit-storage.md)できます。

  ``--junction`` パラメーターでは次の値が使用されます。

  * ``namespace-path`` - クライアント側の仮想ファイル パス
  * ``nfs-export`` - クライアント側パスに関連付けるストレージ システム エクスポート
  * ``target-path`` (省略可能) - 必要な場合、エクスポートのサブディレクトリ

  例: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  仮想名前空間の機能の詳細については、「[集約された名前空間を構成する](hpc-cache-namespace.md)」を参照してください。

コマンドの例:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

出力:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="next-steps"></a>次のステップ

ストレージ ターゲットの作成後は、次のいずれかのタスクを検討してください。

* [Azure HPC キャッシュをマウントする](hpc-cache-mount.md)
* [Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)

設定を更新する必要がある場合、[ストレージ ターゲットを編集](hpc-cache-edit-storage.md)できます。
