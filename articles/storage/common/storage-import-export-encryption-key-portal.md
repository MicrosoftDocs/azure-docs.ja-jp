---
title: Azure portal を使用して、Import/Export サービス用にカスタマー マネージド キーを構成する
description: Azure portal を使用して、Azure Import/Export サービス用に、Azure Key Vault でカスタマー マネージド キーを構成する方法について学びます。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d0a1826dafd1e6ce6202dc4f29417a1ce100e54f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195247"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Azure Key Vault でカスタマー マネージド キーを Import/Export サービスのために使用する

Azure Import/Export では暗号化キーを使用して、ドライブのロックに使用される BitLocker キーが保護されます。 既定では、BitLocker キーは Microsoft マネージド キーで暗号化されます。 暗号化キーをさらに制御するために、カスタマー マネージド キーを提供することもできます。

カスタマー マネージド キーは、Azure Key Vault で作成して格納する必要があります。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../../key-vault/general/overview.md)

この記事では、[Azure portal](https://portal.azure.com/) の Import/Export サービスで、カスタマー マネージド キーを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. 次の手順に従って、インポート ジョブまたはエクスポート ジョブを作成しました。

    - [BLOB のインポート ジョブを作成する](storage-import-export-data-to-blobs.md)
    - [ファイルのインポート ジョブを作成する](storage-import-export-data-to-files.md)
    - [BLOB のエクスポート ジョブを作成する](storage-import-export-data-from-blobs.md)

2. BitLocker キーを保護するために使用できるキーを含む既存の Azure Key Vault があります。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/secrets/quick-create-portal.md)」をご覧ください。

    - 既存の Key Vault には、**論理的な削除**や**消去保護**が設定されています。 これらのプロパティは、既定では有効になっていません。 これらのプロパティを有効にするには、次のいずれかの記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

        - [PowerShell で Key Vault の論理的な削除を使用する方法](../../key-vault/general/soft-delete-powershell.md)
        - [CLI で Key Vault の論理的な削除を使用する方法](../../key-vault/general/soft-delete-cli.md)
    - 既存のキー コンテナーには、2048 サイズ以上の RSA キーが必要です。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)」の「**Key Vault のキー**」を参照してください。
    - キー コンテナーは、データのストレージ アカウントと同じリージョンに存在する必要があります。  
    - 既存の Azure Key Vault がない場合は、次のセクションで説明するように、インラインで作成することもできます。

## <a name="enable-keys"></a>キーを有効にする

Import/Export サービス用のカスタマー マネージド キーの構成は省略できます。 既定では、Import/Export サービスでは、Microsoft マネージド キーを使用して BitLocker キーが保護されます。 Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. インポート ジョブの **[概要]** ブレードに移動します。
2. 右側のウィンドウで、 **[Choose how your BitLocker keys are encrypted]\(BitLocker キーの暗号化方法を選択します\)** を選択します。

    ![暗号化オプションを選択する](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. **[暗号化]** ブレードで、デバイスの BitLocker キーを表示およびコピーできます。 **[暗号化の種類]** で、BitLocker キーを保護する方法を選択できます。 既定では、Microsoft マネージド キーが使用されます。

    ![BitLocker キーを表示する](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. カスタマー マネージド キーを指定するオプションがあります。 カスタマー マネージド キーを選択してから、**キー コンテナーとキーを選択します**。

    ![カスタマー マネージド キーを選択する](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. **[Azure Key Vault からのキーの選択]** ブレードで、サブスクリプションが自動的に設定されます。 **[キー コンテナー]** で、ドロップダウン リストから既存のキー コンテナーを選択できます。

    ![Azure Key Vault を選択または作成する](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. **[新規作成]** を選択して、新しいキー コンテナーを作成することもできます。 **[キー コンテナーの作成]** ブレードで、リソース グループとキー コンテナーの名前を入力します。 その他のすべての既定値をそのまま使用します。 **[確認および作成]** を選択します。

    ![新しい Azure Key Vault を作成する](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. キー コンテナーに関連付けられている情報を確認し、 **[作成]** を選択します。 キー コンテナーの作成が完了するまで数分待ちます。

    ![Azure Key Vault を作成する](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. **[Azure Key Vault からのキーの選択]** ブレードで、既存のキー コンテナー内のキーを選択できます。

9. 新しいキー コンテナーを作成した場合は、 **[新規作成]** を選択してキーを作成します。 RSA キーのサイズは 2048 以上にすることができます。

    ![Azure Key Vault で新しいキーを作成する](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    キー コンテナーを作成するときに論理的な削除と消去保護が有効になっていない場合は、キー コンテナーが更新され、論理的な削除と消去保護が有効になります。

10. キーの名前を指定し、他の既定値をそのまま使用して、 **[作成]** を選択します。

    ![新しいキーを作成する](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. **[バージョン]** を選択し、 **[選択]** を選択します。 キー コンテナー内にキーが作成されたことが通知されます。

    ![キー コンテナーで作成された新しいキー](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

**[暗号化]** ブレードで、ご自分のカスタマー マネージド キーに対してキー コンテナーとキーが選択されていることを確認できます。

> [!IMPORTANT]
> インポート/エクスポート ジョブのどの段階でも、Microsoft マネージド キーを無効にして、カスタマー マネージド キーに移動することは可能です。 ただし、カスタマー マネージド キーは、作成後に無効にすることはできません。

## <a name="troubleshoot-customer-managed-key-errors"></a>カスタマー マネージド キーのエラーをトラブルシューティングする

カスタマー マネージド キーに関連するエラーが発生した場合は、次の表を使用してトラブルシューティングを行ってください。

| エラー コード     |詳細     | 回復可能かどうか    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | カスタマー マネージド キーへのアクセスが取り消されています。                                                       | 回復可能。次のことを確認してください。 <ol><li>キー コンテナーのアクセス ポリシーに引き続き MSI が含まれている。</li><li>アクセス ポリシーで Get、Wrap、および Unwrap のアクセス許可が有効になっている。</li><li>キー コンテナーがファイアウォールの内側の VNet にある場合は、 **[信頼された Microsoft サービスを許可]** が有効になっているかどうかを確認する。</li><li>ジョブ リソースの MSI が API を使用して `None` にリセットされたかどうかを確認する。<br>「はい」の場合は、値を `Identity = SystemAssigned` に戻します。 これにより、ジョブ リソースの ID が再作成されます。<br>新しい ID が作成されたら、キー コンテナーのアクセス ポリシーで新しい ID に対して `Get`、`Wrap`、`Unwrap` のアクセス許可を有効にします。</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | カスタマー マネージド キーが無効になっています。                                         | 回復可能 (キー バージョンを有効にした場合)。     |
| CmkErrorKeyNotFound      | カスタマー マネージド キーが見つかりません。 | 回復可能。キーが削除されているが、まだ消去期間内であれば、[Undo Key vault key removal](https://docs.microsoft.com/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval) (キー コンテナーのキーの削除を元に戻す) を使用します。<br>または <ol><li>回復可能 (顧客がキーをバックアップして復元している場合)。</li><li>回復不可能 (それ以外の場合)。</li></ol>
| CmkErrorVaultNotFound |カスタマー マネージド キーのキー コンテナーが見つかりません。 |   キー コンテナーが削除されている場合は、以下のとおりです。<ol><li>回復可能。消去保護期間内であれば、「[キー コンテナーの復旧](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault)」にある手順を利用します。</li><li>回復不可能。消去保護期間を超えている場合。</li></ol><br>または、キー コンテナーが別のテナントに移行された場合は、回復可能。次のいずれかの手順を使って回復できます。<ol><li>キー コンテナーを古いテナントに戻します。</li><li>`Identity = None` を設定し、値を `Identity = SystemAssigned` に戻します。 これにより、ID が削除され、新しい ID の作成後に再登録されます。 キー コンテナーのアクセス ポリシーで新しい ID の `Get`、`Wrap`、`Unwrap` アクセス許可を有効にします。</li></ol>|

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
