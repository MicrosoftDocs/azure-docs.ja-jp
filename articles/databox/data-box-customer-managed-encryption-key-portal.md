---
title: Azure portal を使用して Azure Data Box 用のカスタマー マネージド キーを管理する
description: Azure portal を使用して、Azure Data Box 用に、Azure Key Vault でカスタマー マネージド キーを作成および管理する方法について学びます。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: e6b588ddea5bf4b4c92e89d9cebb37b09b9a86af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791546"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Azure Key Vault のカスタマー マネージド キーを Azure Data Box に使用する

Azure Data Box では、デバイスのロックに使用されるデバイス ロック解除キー (デバイス パスワードとも呼ばれる) が暗号化キーで保護されます。 既定では、この暗号化キーは Microsoft マネージド キーです。 制御を強化するために、カスタマー マネージド キーを使用できます。

カスタマー マネージド キーを使用しても、デバイス上のデータの暗号化方法には影響しません。 デバイスのロック解除キーの暗号化方法にのみ影響します。

注文プロセス全体でこのレベルの制御を維持するには、注文を作成するときにカスタマー マネージド キーを使用します。 詳細については、[Azure Data Box の注文](data-box-deploy-ordered.md)に関するページを参照してください。

この記事では、[Azure portal](https://portal.azure.com/) で、カスタマー マネージド キーを既存の Data Box の注文で有効にする方法について説明します。 現在のカスタマー マネージド キーのキーコンテナー、キー、バージョン、または ID を変更する方法、および Microsoft マネージド キーの使用に戻す方法について知ることができます。

この記事は、Azure Data Box と Azure Data Box Heavy の両方のデバイスに適用されます。

## <a name="requirements"></a>必要条件

Data Box 注文用のカスタマー マネージド キーは、次の要件を満たしている必要があります。

- キーは、 **[論理的な削除]** と **[消去しない]** が有効になっている Azure Key Vault で作成および保存する必要があります。 詳細については、「 [Azure Key Vault とは](../key-vault/general/overview.md)」を参照してください。 キー コンテナーとキーは、注文を作成または更新する際に作成できます。

- これは、サイズが 2048 以上の RSA キーにする必要があります。

## <a name="enable-key"></a>キーを有効にする

Azure portal で、カスタマー マネージド キーを既存の Data Box の注文で有効にするには、次の手順を実行します。

1. Data Box の注文の **[概要]** 画面に移動します。

    ![Data Box の注文の [概要] 画面 - 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. **[設定] > [暗号化]** と移動し、 **[カスタマー マネージド キー]** を選択します。 次に、 **[キーとキー コンテナーを選択する]** を選択します。

    ![カスタマー マネージド キーの暗号化オプションを選択する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   **[Azure Key Vault からのキーの選択]** 画面で、サブスクリプションが自動的に設定されます。

 3. **[キー コンテナー]** で、ドロップダウン リストから既存のキー コンテナーを選択するか、 **[新規作成]** を選択して新しいキー コンテナーを作成することができます。

     ![カスタマー マネージド キーを選択する際の [キー コンテナー] オプション](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     新しいキー コンテナーを作成するには、サブスクリプション、リソース グループ、キー コンテナー名、その他の情報を **[新しい Key Vault の作成]** 画面に入力します。 **[回復オプション]** で、 **[論理的な削除]** と **消去保護** が有効になっていることを確認します。 次に、 **[Review + Create]\(確認と作成\)** を選択します。

      ![Azure Key Vault を確認および作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      キー コンテナーの情報を確認し、 **[作成]** を選択します。 キー コンテナーの作成が完了するまで数分待ちます。

       ![設定を使用して Azure Key Vault を作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. **[Azure Key Vault からのキーの選択]** 画面で、キーコンテナーから既存のキーを選択するか、新しいキーを作成することができます。

    ![Azure Key Vault からキーを選択する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   新しいキーを作成する場合は、 **[新規作成]** を選択します。 RSA キーを使用する必要があります。 サイズは 2048 以上にすることができます。

    ![Azure Key Vault で新しいキーを作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    新しいキーの名前を入力し、他の既定値をそのまま使用して、 **[作成]** を選択します。 キー コンテナー内にキーが作成されたことが通知されます。

    ![新しいキーに名前を付ける](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. **[バージョン]** で、ドロップダウン リストから既存のキーのバージョンを選択できます。

    ![新しいキーのバージョンを選択する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    新しいキーのバージョンを生成する場合は、 **[新規作成]** を選択します。

    ![新しいキーのバージョンを作成するためのダイアログ ボックスを開く](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    新しいキー バージョンの設定を選択し、 **[作成]** を選択します。

    ![新しいキー バージョンを作成する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. キー コンテナー、キー、およびキーのバージョンを選択したら、 **[選択]** を選択します。

    ![Azure Key Vault のキー](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    **[暗号化の種類]** の設定に、選択したキー コンテナーとキーが表示されます。

    ![カスタマー マネージド キーのキーとキー コンテナー](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. このリソースのカスタマー マネージド キーを管理するために使用する ID の種類を選択します。 注文の作成時に生成された **システム割り当て** ID を使用することも、ユーザー割り当て ID を選択することもできます。

    ユーザー割り当て ID は、リソースへのアクセスの管理に使用できる独立したリソースです。 詳細については、[マネージド ID の種類](../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。

    ![ID の種類を選択する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    ユーザー ID を割り当てるには、 **[ユーザー割り当て]** を選択します。 次に、 **[ユーザー ID を選択する]** を選択し、使用するマネージド ID を選択します。

    ![使用する ID を選択する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    ここでは、新しい ユーザー ID を作成することはできません。 作成方法については、「[Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)」を参照してください。

    選択したユーザー ID が **[暗号化の種類]** の設定に表示されます。

    ![選択したユーザー ID が [暗号化の種類] の設定に表示される](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. **[保存]** を選択して、更新した **暗号化の種類** の設定を保存します。

     ![カスタマー マネージド キーを保存する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    キーの URL が、 **[暗号化の種類]** の下に表示されます。

    ![カスタマー マネージド キーの URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>キーを変更する

現在使用しているカスタマー マネージド キーのキーコンテナー、キー、キーのバージョンを変更するには、次の手順を実行します。

1. Data Box の注文の **[概要]** 画面で、 **[設定]**  >  **[暗号化]** の順に移動して、 **[キーの変更]** をクリックします。

    ![カスタマー マネージド キーを使用した Data Box の注文の [概要] 画面 - 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. **[別のキー コンテナーとキーを選択する]** を選択します。

    ![Data Box の注文の [概要] 画面の [別のキーとキー コンテナーを選択する] オプション](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. **[Key Vault からのキーの選択]** 画面にはサブスクリプションが表示されますが、キー コンテナー、キー、またはキーのバージョンは表示されません。 次の変更を行うことができます。

   - 同じキー コンテナーから別のキーを選択する。 キーとバージョンを選択する前に、キー コンテナーを選択する必要があります。

   - 別のキー コンテナーを選択して、新しいキーを割り当てる。

   - 現在のキーのバージョンを変更する。
   
    変更が完了したら、 **[選択]** を選択します。

    ![暗号化オプションを選択する - 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. **[保存]** を選択します。

    ![更新した暗号化の設定を保存する - 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>ID を変更する

この注文のカスタマー マネージド キーへのアクセスの管理に使用する ID を変更するには、次の手順を実行します。

1. 完了した Data Box の注文の **[概要]** 画面で、 **[設定]**  >  **[暗号化]** の順に移動します。

2. 次のいずれかの変更を行います。

     - 別のユーザー ID に変更するには、 **[別のユーザー ID を選択する]** をクリックします。 次に、画面の右側にあるパネルで別の ID を選択し、 **[選択]** を選択します。

       ![カスタマー マネージド キーのユーザー割り当て ID を変更するためのオプション](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - 注文の作成時に生成されたシステム割り当て ID に切り替えるには、 **[ID の種類の選択]** で **[システム割り当て]** を選択します。

     ![カスタマー マネージド キーでシステム割り当てに変更するためのオプション](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. **[保存]** を選択します。

    ![更新した暗号化の設定を保存する - 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Microsoft マネージド キーを使用する

注文で使用するキーをカスタマー マネージド キーから Microsoft マネージド キーに変更するには、次の手順を実行します。

1. 完了した Data Box の注文の **[概要]** 画面で、 **[設定]**  >  **[暗号化]** の順に移動します。

2. **[種類の選択]** で **[Microsoft マネージド キー]** を選択します。

    ![Data Box の注文の [概要] 画面 - 5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. **[保存]** を選択します。

    ![更新した暗号化の設定 (Microsoft マネージド キー) を保存する](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>エラーをトラブルシューティングする

カスタマー マネージド キーに関連するエラーが発生した場合は、次の表を使用してトラブルシューティングを行ってください。

| エラー コード| エラーの詳細| 回復可能かどうか|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| カスタマー マネージド キーが無効化されたため、パスキーをフェッチできませんでした。| 回復可能 (キー バージョンを有効にした場合)。|
| SsemUserErrorEncryptionKeyExpired| カスタマー マネージド キーの有効期限が切れたため、パスキーをフェッチできませんでした。| 回復可能 (キー バージョンを有効にした場合)。|
| SsemUserErrorKeyDetailsNotFound| カスタマー マネージド キーが見つからなかったため、パスキーをフェッチできませんでした。| キー コンテナーを削除した場合は、カスタマー マネージド キーを復旧できません。  キー コンテナーを別のテナントに移行した場合は、「[サブスクリプション移行後のキー コンテナー テナント ID の変更](../key-vault/general/move-subscription.md)」を参照してください。 キー コンテナーを削除した場合:<ol><li>回復可能。消去保護期間内であれば、「[キー コンテナーの復旧](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)」にある手順を利用します。</li><li>回復不可能。消去保護期間を超えている場合。</li></ol><br>それ以外の場合、キー コンテナーがテナント移行の対象になっていた場合は回復できます。下のいずれかの手順で回復できます。 <ol><li>キー コンテナーを古いテナントに戻します。</li><li>`Identity = None` を設定し、値を `Identity = SystemAssigned` に戻します。 これにより、ID が削除され、新しい ID の作成後に再登録されます。 キー コンテナーのアクセス ポリシーで新しい ID の `Get`、`Wrap`、`Unwrap` アクセス許可を有効にします。</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | カスタマー マネージド キーが適用されましたが、キーへのアクセスが許可されていないか、無効になっているか、ファイアウォールが有効になっているためキー コンテナーにアクセスできませんでした。 | カスタマー マネージド キーへのアクセスを有効にするには、選択した ID をキー コンテナーに追加します。 キー コンテナーでファイアウォールが有効になっている場合は、システム割り当て ID に切り替えて、カスタマー マネージド キーを追加します。 詳細については、[キーを有効にする方法](#enable-key)に関する記事を参照してください。 |
| SsemUserErrorKeyVaultDetailsNotFound| カスタマー マネージド キーの関連づけられているキー コンテナーが見つからなかったため、パスキーをフェッチできませんでした。 | キー コンテナーを削除した場合は、カスタマー マネージド キーを復旧できません。  キー コンテナーを別のテナントに移行した場合は、「[サブスクリプション移行後のキー コンテナー テナント ID の変更](../key-vault/general/move-subscription.md)」を参照してください。 キー コンテナーを削除した場合:<ol><li>回復可能。消去保護期間内であれば、「[キー コンテナーの復旧](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)」にある手順を利用します。</li><li>回復不可能。消去保護期間を超えている場合。</li></ol><br>それ以外の場合、キー コンテナーがテナント移行の対象になっていた場合は回復できます。下のいずれかの手順で回復できます。 <ol><li>キー コンテナーを古いテナントに戻します。</li><li>`Identity = None` を設定し、値を `Identity = SystemAssigned` に戻します。 これにより、ID が削除され、新しい ID の作成後に再登録されます。 キー コンテナーのアクセス ポリシーで新しい ID の `Get`、`Wrap`、`Unwrap` アクセス許可を有効にします。</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | カスタマー マネージド キーが見つからなかったため、パスキーをフェッチできませんでした。| 回復可能。次のことを確認してください。 <ol><li>キー コンテナーのアクセス ポリシーに引き続き MSI が含まれている。</li><li>ID は、システムによって割り当てられたものである。</li><li>キー コンテナーのアクセス ポリシーで、この ID に対して Get、Wrap、および Unwrap のアクセス許可を有効にします。</li></ol>|
| SsemUserErrorUserAssignedLimitReached | 追加可能なユーザー割り当て ID 合計数の最大値に達したため、新しいユーザー割り当て ID を追加できませんでした。 | ユーザーID を減らすか、リソースから一部のユーザー割り当てID を削除してから、操作を再試行してください。 |
| SsemUserErrorCrossTenantIdentityAccessForbidden | マネージド ID アクセス操作に失敗しました。 <br> 注:これは、サブスクリプションが別のテナントに移動された場合のシナリオを対象としています。 顧客は、ID を新しいテナントに手動で移動する必要があります。 詳細については、PFA メールを参照してください。 | 選択した ID は、サブスクリプションが存在する新しいテナントに移動してください。 詳細については、[キーを有効にする方法](#enable-key)に関する記事を参照してください。 |
| SsemUserErrorKekUserIdentityNotFound | カスタマー マネージド キーが適用されましたが、キーにアクセス可能なユーザー割り当て ID が Active Directory に見つかりませんでした。 <br> 注:これは、ユーザー ID が Azure から削除された場合を対象としています。| カスタマー マネージド キーへのアクセスを有効にするには、選択した別のユーザー割り当て ID をキー コンテナーに追加してみてください。 詳細については、[キーを有効にする方法](#enable-key)に関する記事を参照してください。 |
| SsemUserErrorUserAssignedIdentityAbsent | カスタマー マネージド キーが見つからなかったため、パスキーをフェッチできませんでした。 | カスタマー マネージド キーにアクセスできませんでした。 キーに関連付けられているユーザー割り当て ID (UAI) が削除されているか、UAI の種類が変更されています。 |
| SsemUserErrorCrossTenantIdentityAccessForbidden | マネージド ID アクセス操作に失敗しました。 <br> 注:これは、サブスクリプションが別のテナントに移動された場合のシナリオを対象としています。 顧客は、ID を新しいテナントに手動で移動する必要があります。 詳細については、PFA メールを参照してください。 | カスタマー マネージド キーへのアクセスを有効にするには、選択した別のユーザー割り当て ID をキー コンテナーに追加してみてください。 詳細については、[キーを有効にする方法](#enable-key)に関する記事を参照してください。|
| SsemUserErrorKeyVaultBadRequestException | カスタマー マネージド キーが適用されましたが、キーへのアクセスが許可されていないか、無効になっているか、ファイアウォールが有効になっているためキー コンテナーにアクセスできませんでした。 | カスタマー マネージド キーへのアクセスを有効にするには、選択した ID をキー コンテナーに追加します。 キー コンテナーでファイアウォールが有効になっている場合は、システム割り当て ID に切り替えて、カスタマー マネージド キーを追加します。 詳細については、[キーを有効にする方法](#enable-key)に関する記事を参照してください。 |
| 一般的なエラー  | パスキーをフェッチできませんでした。| これは一般的なエラーです。 エラーをトラブルシューティングして、次の手順を決定するには、Microsoft サポートにお問い合わせください。|

## <a name="next-steps"></a>次のステップ

- [Azure Key Vault とは](../key-vault/general/overview.md)
- [クイック スタート:Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/secrets/quick-create-portal.md)
