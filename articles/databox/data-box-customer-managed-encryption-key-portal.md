---
title: Azure portal を使用して Azure Data Box 用にカスタマー マネージド キーを構成する
description: Azure portal を使用して、Azure Data Box 用に、Azure Key Vault でカスタマー マネージド キーを構成する方法について学びます。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 60e621b34250b036888b233b084ba1ddff939048
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087800"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Azure Key Vault のカスタマー マネージド キーを Azure Data Box に使用する

Azure Data Box は、デバイスのロックに使用されたデバイス ロック解除キー (デバイス パスワードとも呼ばれる) を、暗号化キーにより保護します。 既定では、Data Box の注文のデバイス ロック解除キーは、Microsoft マネージド キーを使って暗号化されます。 デバイス ロック解除キーをさらに制御するために、カスタマー マネージド キーを提供することもできます。 

カスタマー マネージド キーは、Azure Key Vault で作成して格納する必要があります。 Azure Key Vault の詳細については、「[Azure Key Vault とは](../key-vault/general/overview.md)」をご覧ください。

この記事では、[Azure portal](https://portal.azure.com/) で Azure Data Box にカスタマー マネージド キーを使用する方法について説明します。 この記事は Azure Data Box デバイスと Azure Data Box Heavy デバイスの両方に適用されます。

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. 次の指示に従って、Azure Data Box の注文を作成していること: [チュートリアル: Azure Data Box を注文する](data-box-deploy-ordered.md)を完了していること。

2. デバイス ロック解除キーを保護するために使用できるキーを含む既存の Azure Key Vault があること。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/secrets/quick-create-portal.md)」をご覧ください。

    - 既存のキー コンテナーには、 **[論理的な削除]** と **[Do not purge]\(消去しない\)** が設定されています。 これらのプロパティは、既定では有効になっていません。 これらのプロパティを有効にするには、次のいずれかの記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

        - [PowerShell で Key Vault の論理的な削除を使用する方法](../key-vault/general/soft-delete-powershell.md)
        - [CLI で Key Vault の論理的な削除を使用する方法](../key-vault/general/soft-delete-cli.md)
    - 既存のキー コンテナーには、2048 サイズ以上の RSA キーが必要です。 キーの詳細については、「[Azure Key Vault のキーについて](../key-vault/keys/about-keys.md)」を参照してください。
    - キー コンテナーは、データに使用されるストレージ アカウントと同じリージョンに存在している必要があります。 複数のストレージ アカウントを Azure Data Box リソースとリンクできます。
    - 既存のキー コンテナーがない場合は、次のセクションで説明するように、インラインで作成することもできます。

## <a name="enable-keys"></a>キーを有効にする

Azure Data Box のカスタマー マネージド キーの構成は省略可能です。 Data Box では、既定で Microsoft マネージド キーを使用して BitLocker キーが保護されます。 Azure portal でカスタマー マネージド キーを有効にするには、次の手順に従ってください。

1. Data Box の注文の **[概要]** ブレードに移動します。

    ![Data Box の注文の [概要] ブレード](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. **[設定] > [暗号化]** に移動します。 **[暗号化の種類]** で、デバイス ロック解除キーを保護する方法を選択できます。 既定では、Microsoft マネージド キーを使用してデバイス ロック解除パスワードが保護されます。 

    ![暗号化オプションを選択する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. 暗号化の種類として、 **[カスタマー マネージド キー]** を選択します。 カスタマー マネージド キーを選択してから、**キー コンテナーとキーを選択します**。

    ![カスタマー マネージド キーを選択する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. **[Azure Key Vault からのキーの選択]** ブレードで、サブスクリプションが自動的に設定されます。 **[キー コンテナー]** で、ドロップダウン リストから既存のキー コンテナーを選択できます。

    ![新しい Azure Key Vault を作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    **[新規作成]** を選択して、新しいキー コンテナーを作成することもできます。 **[キー コンテナーの作成]** ブレードで、リソース グループとキー コンテナーの名前を入力します。 **[論理的な削除]** と **[消去保護]** が有効になっていることを確認します。 その他のすべての既定値をそのまま使用します。 **[確認および作成]** を選択します。

    ![新しい Azure Key Vault を作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. キー コンテナーに関連付けられている情報を確認し、 **[作成]** を選択します。 キー コンテナーの作成が完了するまで数分待ちます。

    ![Azure Key Vault を作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. **[Azure Key Vault からのキーの選択]** ブレードで、既存のキー コンテナー内のキーを選択できます。

    ![Azure Key Vault で新しいキーを作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. 新しいキーを作成する場合は、 **[新規作成]** を選択してキーを作成します。 RSA キーのサイズは 2048 以上にすることができます。

    ![Azure Key Vault で新しいキーを作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. キーの名前を指定し、他の既定値をそのまま使用して、 **[作成]** を選択します。 

    ![新しいキーを作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. キー コンテナー内にキーが作成されたことが通知されます。 **[バージョン]** を選択し、 **[選択]** を選択します。

    ![キー コンテナーで作成された新しいキー](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. **[暗号化の種類]** ペインで、カスタマー マネージド キー用に選択されているキー コンテナーとキーを確認できます。

    ![カスタマー マネージド キーのキーとキー コンテナー](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. キーを保存します。 

    ![カスタマー マネージド キーを保存する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    キーの URL が、 **[暗号化の種類]** の下に表示されます。

    ![カスタマー マネージド キーの URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Data Box の注文のどの段階でも、Microsoft マネージド キーを無効にして、カスタマー マネージド キーに移行できます。 ただし、カスタマー マネージド キー作成した後は、Microsoft マネージド キーに戻すことはできません。

## <a name="troubleshoot-errors"></a>エラーをトラブルシューティングする

カスタマー マネージド キーに関連するエラーが発生した場合は、次の表を使用してトラブルシューティングを行ってください。

| エラー コード| エラーの詳細| 回復可能かどうか|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| カスタマー マネージド キーが無効化されたため、パスキーをフェッチできませんでした。| 回復可能 (キー バージョンを有効にした場合)。|
| SsemUserErrorEncryptionKeyExpired| カスタマー マネージド キーの有効期限が切れたため、パスキーをフェッチできませんでした。| 回復可能 (キー バージョンを有効にした場合)。|
| SsemUserErrorKeyDetailsNotFound| カスタマー マネージド キーが見つからなかったため、パスキーをフェッチできませんでした。| キー コンテナーを削除した場合は、カスタマー マネージド キーを復旧できません。  キー コンテナーを別のテナントに移行した場合は、「[サブスクリプション移行後のキー コンテナー テナント ID の変更](../key-vault/general/move-subscription.md)」を参照してください。 キー コンテナーを削除した場合:<ol><li>回復可能。消去保護期間内であれば、「[キー コンテナーの復旧](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)」にある手順を利用します。</li><li>回復不可能。消去保護期間を超えている場合。</li></ol><br>それ以外の場合、キー コンテナーがテナント移行の対象になっていた場合は回復できます。下のいずれかの手順で回復できます。 <ol><li>キー コンテナーを古いテナントに戻します。</li><li>`Identity = None` を設定し、値を `Identity = SystemAssigned` に戻します。 これにより、ID が削除され、新しい ID の作成後に再登録されます。 キー コンテナーのアクセス ポリシーで新しい ID の `Get`、`Wrap`、`Unwrap` アクセス許可を有効にします。</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| カスタマー マネージド キーのアクセスが取り消されたため、パスキーをフェッチできませんでした。| 回復可能。次のことを確認してください。 <ol><li>キー コンテナーのアクセス ポリシーに引き続き MSI が含まれている。</li><li>アクセス ポリシーで、Get、Wrap、Unwrap へのアクセス許可が指定されている。</li><li>キー コンテナーがファイアウォールの内側の VNet にある場合は、 **[信頼された Microsoft サービスを許可]** が有効になっているかどうかを確認します。</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| カスタマー マネージド キーの関連づけられているキー コンテナーが見つからなかったため、パスキーをフェッチできませんでした。 | キー コンテナーを削除した場合は、カスタマー マネージド キーを復旧できません。  キー コンテナーを別のテナントに移行した場合は、「[サブスクリプション移行後のキー コンテナー テナント ID の変更](../key-vault/general/move-subscription.md)」を参照してください。 キー コンテナーを削除した場合:<ol><li>回復可能。消去保護期間内であれば、「[キー コンテナーの復旧](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)」にある手順を利用します。</li><li>回復不可能。消去保護期間を超えている場合。</li></ol><br>それ以外の場合、キー コンテナーがテナント移行の対象になっていた場合は回復できます。下のいずれかの手順で回復できます。 <ol><li>キー コンテナーを古いテナントに戻します。</li><li>`Identity = None` を設定し、値を `Identity = SystemAssigned` に戻します。 これにより、ID が削除され、新しい ID の作成後に再登録されます。 キー コンテナーのアクセス ポリシーで新しい ID の `Get`、`Wrap`、`Unwrap` アクセス許可を有効にします。</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | カスタマー マネージド キーが見つからなかったため、パスキーをフェッチできませんでした。| 回復可能。次のことを確認してください。 <ol><li>キー コンテナーのアクセス ポリシーに引き続き MSI が含まれている。</li><li>ID は、システムによって割り当てられたものである。</li><li>キー コンテナーのアクセス ポリシーで、この ID に対して Get、Wrap、および Unwrap のアクセス許可を有効にします。</li></ol>|
| 一般的なエラー  | パスキーをフェッチできませんでした。| これは一般的なエラーです。 エラーをトラブルシューティングして、次の手順を決定するには、Microsoft サポートにお問い合わせください。|


## <a name="next-steps"></a>次のステップ

- [Azure Key Vault とは](../key-vault/general/overview.md)