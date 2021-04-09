---
title: Azure HPC Cache ストレージ ターゲットを更新する
description: Azure HPC Cache ストレージ ターゲットを編集する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/10/2021
ms.author: v-erkel
ms.openlocfilehash: 0c505937d4adbe2596e91ed7269676e60ada8253
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772589"
---
# <a name="edit-storage-targets"></a>ストレージ ターゲットを編集する

Azure portal または Azure CLI を使用して、ストレージ ターゲットを削除または変更できます。

ストレージの種類に応じて、次のストレージ ターゲット値を変更できます。

* BLOB ストレージ ターゲットの場合は、名前空間のパスとアクセス ポリシーを変更できます。

* NFS ストレージ ターゲットの場合は、次の値を変更できます。

  * 名前空間パス
  * アクセス ポリシー
  * 名前空間パスに関連付けられているストレージ エクスポートまたはエクスポート サブディレクトリ
  * 使用モデル

* ADLS-NFS ストレージ ターゲットの場合は、名前空間のパス、アクセス ポリシー、使用モデルを変更できます。

ストレージ ターゲットの名前、種類、またはバックエンド ストレージ システム (Blob コンテナーまたは NFS ホスト名/IP アドレス) を編集することはできません。 これらのプロパティを変更する必要がある場合は、ストレージ ターゲットを削除し、新しい値で置換を作成します。

> [!TIP]
> [Azure HPC Cache の管理ビデオ](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)では、Azure portal でストレージ ターゲットを編集する方法を確認できます。

## <a name="remove-a-storage-target"></a>ストレージ ターゲットの削除

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ストレージ ターゲットを削除するには、 **[ストレージ ターゲット]** ページを開きます。 一覧からストレージ ターゲットを選択し、 **[削除]** ボタンをクリックします。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

[az hpc-cache storage-target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) を使用し、キャッシュからストレージ ターゲットを削除します。

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

ストレージ ターゲットを削除することにより、この Azure HPC Cache システムとのストレージ システムの関連付けが削除されますが、バックエンド ストレージ システムは変更されません。 たとえば、Azure BLOB ストレージ コンテナーを使用した場合、コンテナーとその内容はキャッシュから削除された後も存在します。 コンテナーを別の Azure HPC Cache に追加したり、このキャッシュに再度追加したり、Azure portal で削除したりすることができます。

キャッシュに格納されているファイルの変更は、ストレージ ターゲットが削除される前に、バックエンド ストレージ システムに書き込まれます。 キャッシュ内に多数の変更されたデータがある場合、このプロセスには 1 時間以上かかることがあります。

## <a name="change-a-blob-storage-targets-namespace-path"></a>BLOB ストレージ ターゲットの名前空間パスを変更する

名前空間パスは、クライアントがこのストレージ ターゲットをマウントするために使用するパスです。 (詳細については、「[集約された名前空間を計画する](hpc-cache-namespace.md)」および「[集約された名前空間を設定する](add-namespace-paths.md)」をご覧ください。)

名前空間パスは、Azure Blob ストレージ ターゲットで行うことができる唯一の更新です。 これを変更するには、Azure portal または Azure CLI を使用します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ご利用の Azure HPC Cache の **[名前空間]** ページを使用します。 名前空間のページについては、記事「[集約された名前空間を設定する](add-namespace-paths.md)」で詳細に説明しています。

変更するパスの名前をクリックし、表示される編集ウィンドウで新しいパスを作成します。

![BLOB 名前空間パスをクリックした後の名前空間ページのスクリーンショット。右側のペインに編集フィールドが表示されています](media/update-namespace-blob.png)

変更を行った後、 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** をクリックして変更を破棄します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

BLOB ストレージ ターゲットの名前空間を Azure CLI を使用して変更するには、コマンド [az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) を使用します。 `--virtual-namespace-path` 値のみを変更できます。

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>NFS ストレージ ターゲットを更新する

NFS ストレージ ターゲットの場合は、仮想名前空間パスを変更または追加したり、名前空間パスが指す NFS エクスポートまたはサブディレクトリの値を変更したり、使用モデルを変更したりできます。

一部の種類のカスタム DNS 設定が指定されたキャッシュ内のストレージ ターゲットには、IP アドレスを更新するためのコントロールもあります。 (この種類の構成はまれです。)

詳細については以下にあります。

* 「[集約された名前空間値を変更する](#change-aggregated-namespace-values)」 (仮想名前空間パス、アクセス ポリシー、エクスポート、エクスポート サブディレクトリ)
* 「[使用モデルを変更する](#change-the-usage-model)」
* [DNS を更新する](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>集約された名前空間値を変更する

Azure portal または Azure CLI を使用して、クライアント向けの名前空間パス、ストレージ エクスポート、エクスポート サブディレクトリ (使用されている場合) を変更できます。

1 つのストレージ ターゲットに複数の有効なパスを作成する方法について再確認する必要がある場合は、[NFS 名前空間パスの追加](add-namespace-paths.md#nfs-namespace-paths)に関するガイドラインを参照してください。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ご利用の Azure HPC Cache の **[名前空間]** ページを使用して、名前空間の値を更新します。 このページについては、記事「[集約された名前空間を設定する](add-namespace-paths.md)」で詳細に説明しています。

![右側に NFS 更新ページが開いているポータルの名前空間ページのスクリーンショット](media/update-namespace-nfs.png)

1. 変更するパスの名前をクリックします。
1. [編集] ウィンドウを使用して、新しい仮想パス、エクスポート、またはサブディレクトリの値を入力するか、別のアクセス ポリシーを選択します。
1. 変更を加えた後、 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** で変更を破棄します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

[az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) コマンドで ``--junction`` オプションを使用して、名前空間パス、NFS エクスポート、またはエクスポート サブディレクトリを変更します。

``--junction`` パラメーターでは次の値が使用されます。

* ``namespace-path`` - クライアント側の仮想ファイル パス
* ``nfs-export`` - クライアント側パスに関連付けるストレージ システム エクスポート
* ``target-path`` (省略可能) - 必要な場合、エクスポートのサブディレクトリ

例: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

``--junction`` ステートメントには、各パスの 3 つの値すべてを指定する必要があります。 変更しない値には、既存の値を使用します。

キャッシュ名、ストレージ ターゲット名、およびリソース グループも、すべての更新コマンドで必須です。

コマンドの例:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>使用モデルを変更する

使用モデルは、キャッシュによってデータが保持される方法に影響します。 詳細については、「[使用モデルを選択する](hpc-cache-add-storage.md#choose-a-usage-model)」を参照してください。

NFS ストレージ ターゲットの使用モデルを変更するには、次のいずれかの方法を使用します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の **[ストレージ ターゲット]** ページで、使用モデルを変更します。 変更するストレージ ターゲットの名前をクリックします。

![NFS ストレージ ターゲットの編集ページのスクリーンショット](media/edit-storage-nfs.png)

ドロップダウン セレクターを使用して、新しい使用モデルを選択します。 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** をクリックして変更を破棄します。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache 向けに Azure CLI を設定します](./az-cli-prerequisites.md)。

[az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) コマンドを使用します。

update コマンドは、NFS ストレージ ターゲットを追加するために使用するコマンドとほぼ同じものです。 詳細と例については、「[NFS ストレージ ターゲットを作成する](hpc-cache-add-storage.md#create-an-nfs-storage-target)」を参照してください。

使用モデルを変更するには、``--nfs3-usage-model`` オプションを更新します。 例: ``--nfs3-usage-model WRITE_WORKLOAD_15``

キャッシュ名、ストレージ ターゲット名、およびリソース グループの値も必須です。

使用モデルの名前を確認するには、コマンド [az hpc-cache usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list) を使用します。

キャッシュが停止しているか、正常な状態ではない場合、キャッシュが正常になってから更新が適用されます。

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>IP アドレスの更新 (カスタム DNS 構成のみ)

キャッシュで既定以外の DNS 構成を使用している場合は、バックエンドの DNS の変更により、NFS ストレージ ターゲットの IP アドレスが変更される可能性があります。 DNS サーバーでバックエンド ストレージ システムの IP アドレスが変更された場合、Azure HPC Cache ではストレージ システムにアクセスできなくなる可能性があります。

理想をいえば、キャッシュのカスタム DNS システムのマネージャーと連携して、更新プログラムの計画を立てる必要があります。これらの変更によってストレージが使用できなくなるためです。

ストレージ ターゲットの DNS で指定された IP アドレスを更新する必要がある場合は、ストレージ ターゲットの一覧にボタンがあります。 カスタム DNS サーバーで新しい IP アドレスを照会するには、 **[DNS の更新]** をクリックします。

![ストレージ ターゲットの一覧のスクリーンショット。 1 つのストレージ ターゲットに対して、右端の列の [...] メニューが開き、[削除] と [DNS の更新] の 2 つのオプションが表示されます。](media/refresh-dns.png)

成功の場合、更新には 2 分もかかりません。 更新できるストレージ ターゲットは一度に 1 つだけです。前の操作が完了するまで待ってから、別のものをお試しください。

## <a name="update-an-adls-nfs-storage-target-preview"></a>ADLS-NFS ストレージ ターゲットを更新する (プレビュー)

NFS ターゲットと同様に、ADLS-NFS ストレージ ターゲットの名前空間のパスと使用モデルを変更できます。

### <a name="change-an-adls-nfs-namespace-path"></a>ADLS-NFS 名前空間のパスを変更する

ご利用の Azure HPC Cache の **[名前空間]** ページを使用して、名前空間の値を更新します。 このページについては、記事「[集約された名前空間を設定する](add-namespace-paths.md)」で詳細に説明しています。

![右側に ADS-NFS 更新ページが開いているポータルの名前空間ページのスクリーンショット](media/update-namespace-adls.png)

1. 変更するパスの名前をクリックします。
1. [編集] ウィンドウを使用して、新しい仮想パスを入力するか、アクセス ポリシーを更新します。
1. 変更を加えた後、 **[OK]** をクリックしてストレージ ターゲットを更新するか、 **[キャンセル]** で変更を破棄します。

### <a name="change-adls-nfs-usage-models"></a>ADLS-NFS 使用モデルを変更する

ADLS-NFS 使用モデルの構成は、NFS 使用モデルの選択と同じです。 上記の NFS セクションの「[使用モデルを変更する](#change-the-usage-model)」でポータルでの手順をお読みください。 ADLS-NFS ストレージ ターゲットを更新するための追加のツールは開発中です。


## <a name="next-steps"></a>次のステップ

* これらのオプションの詳細については、「[ストレージ ターゲットを追加する](hpc-cache-add-storage.md)」を参照してください。
* 仮想パスの使用に関するその他のヒントについては、「[集約された名前空間を計画する](hpc-cache-namespace.md)」を参照してください。
