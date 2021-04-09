---
title: Azure DevTest Labs のラボで使用される Azure ストレージ アカウントを暗号化する
description: Azure DevTest Labs のラボで使用される Azure ストレージの暗号化を構成する方法について説明します
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149313"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs のラボで使用される Azure ストレージを暗号化する
Azure DevTest Labs で作成されたすべてのラボは、関連付けられた Azure ストレージ アカウントを使用して作成されます。 ストレージ アカウントは、次の目的で使用されます。 

- 仮想マシンの作成に使用できる[数式](devtest-lab-manage-formulas.md)ドキュメントを格納する。
- 成果物の適用から生成された配置ログと拡張ログを含む成果物の結果を格納する。 
- [仮想ハードディスク (VHD) をアップロードして、ラボでカスタム イメージを作成する。](devtest-lab-create-template.md)
- 仮想マシンまたは環境の作成時の取得を高速化するために、頻繁に使用される[成果物](add-artifact-vm.md)と [Azure Resource Manager テンプレート](devtest-lab-create-environment-from-arm.md)をキャッシュする。

> [!NOTE]
> 上記の情報は、ラボを運用する上で不可欠です。 これは、明示的に削除しない限り、ラボ (およびラボ リソース) の有効期間にわたって保存されます。 これらのリソースを手動で削除すると、ラボ VM の作成でエラーが発生したり、後で使用する数式が破損したりする可能性があります。 

## <a name="locate-the-storage-account-and-view-its-contents"></a>ストレージ アカウントを見つけてその内容を表示する

1. ラボのホーム ページで、 **[概要]** ページの **リソース グループ** を選択します。 そのラボが含まれているリソース グループの **[リソースグループ]** ページが表示されます。 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="[概要] ページでリソース グループを選択する":::
1. ラボの Azure ストレージ アカウントを選択します。 ラボ ストレージ アカウントの名前付け規則は、`a<labNameWithoutInvalidCharacters><4-digit number>` です。 たとえば、ラボ名が `contosolab` である場合、ストレージ アカウント名は `acontosolab7576` になります。 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="ラボのリソース グループ内でストレージ アカウントを選択する":::
3. **[ストレージ アカウント]** ページで、左側のメニューから **[Storage Explorer (プレビュー)]** を選択し、次に **[BLOB CONTAINERS]** を選択して、関連するラボ関連のコンテンツを検索します。 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="ストレージ エクスプローラー (プレビュー)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>ラボ ストレージ アカウントを暗号化する
Azure Storage によって、データがクラウドに永続化されるときに自動的に暗号化されます。 Azure Storage 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 詳細については、「[保存データ向け Azure ストレージの暗号化](../storage/common/storage-service-encryption.md)」をご覧ください。

ラボ ストレージ アカウント内のデータは **Microsoft マネージド キー** で暗号化されます。 Microsoft マネージド キーを利用してデータを暗号化することも、独自のキーで暗号化を管理することもできます。 ラボのストレージ アカウントに対して独自のキーを使用して暗号化を管理する場合は、Azure Key Vault で **カスタマー マネージド キー** を指定して、Blob Storage および Azure Files でのデータの暗号化と暗号化の解除に使用できます。 カスタマー マネージド キーの詳細については、「[Azure Key Vault でカスタマー マネージド キーを使用して Azure Storage の暗号化を管理する](../storage/common/customer-managed-keys-overview.md)」を参照してください。

Azure Storage 暗号化用のカスタマー マネージド キーを構成する方法については、次の記事を参照してください。 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Azure Blob Storage のライフ サイクルを管理する
前述のように、ラボのストレージ アカウントに格納されている情報は、ラボがエラーなしで動作するために不可欠です。 このデータは、明示的に削除しない限り、データの種類に応じて、ラボの有効期間中、または特定のラボ仮想マシンの有効期間中、ラボのストレージ アカウントに残ります。

### <a name="uploaded-vhds"></a>アップロードされた VHD
これらの VHD は、カスタム イメージの作成に使用されます。 これらの VHD を削除すると、これらの VHD からカスタム イメージを作成できなくなります。

### <a name="artifacts-cache"></a>成果物キャッシュ
これらのキャッシュは、成果物が適用されるたびに再作成されます。 これらは、参照されている各リポジトリからの最新のコンテンツで更新されます。 そのため、ストレージ関連の経費を節約するためにこの情報を削除しても、救済は一時的なものになります。

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager テンプレート キャッシュ
これらのキャッシュは、Azure Resource Manager ベースのテンプレート リポジトリがラボで接続されて起動されるたびに再作成されます。 これらは、参照されている各リポジトリからの最新のコンテンツで更新されます。 そのため、ストレージ関連の経費を節約するためにこの情報を削除しても、救済は一時的なものになります。

### <a name="formulas"></a>フォーミュラ
これらのドキュメントは、既存の VM からの数式の作成と、数式からの VM の作成の両方のオプションをサポートするために使用されます。 これらの数式ドキュメントを削除すると、次の操作の実行中にエラーが発生する可能性があります。

- 既存のラボ VM からの数式の作成
- 数式の作成または更新 
- 数式からの VM の作成

### <a name="artifact-results"></a>成果物の結果
成果物が適用されると、ラボ VM で実行されている成果物の数と種類に応じて、それぞれの成果物の結果のサイズが時間の経過と共に増加することがあります。 そのため、ラボの所有者は、このようなドキュメントのライフサイクルを制御することができます。 詳細については、「[Azure Blob Storage のライフサイクルの管理](../storage/blobs/storage-lifecycle-management-concepts.md)」を参照してください。

> [!IMPORTANT]
> Azure Storage アカウントに関連する経費を削減するには、この手順を行うことをお勧めします。 

たとえば、次のルールを使用して、成果物の結果に対して 90 日の有効期限ルールを設定します。 これにより、古い成果物の結果がストレージ アカウントから定期的にリサイクルされます。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ
Azure Storage 暗号化用のカスタマー マネージド キーを構成する方法については、次の記事を参照してください。 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)