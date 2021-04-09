---
title: Azure portal でアカウントを作成する
description: Azure ポータルで、クラウド内で大規模な並列ワークロードを実行する Azure Batch アカウントを作成する方法について説明します
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36759a0caef41af9307bf621a1b6b634ddf586cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703666"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure Portal で Batch アカウントを作成する

このトピックでは、[Azure portal](https://portal.azure.com) で、自分のコンピューティング シナリオに適したアカウント プロパティを選択して [Azure Batch アカウント](accounts.md)を作成する方法について説明します。 また、アクセス キーやアカウント URL のような重要なアカウント プロパティを確認できる場所も紹介します。

Batch アカウントとシナリオの背景については、[Batch サービスのワークフローとリソース](batch-service-workflow-features.md)に関する記事を参照してください。

## <a name="create-a-batch-account"></a>Batch アカウントを作成する

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. [Azure portal](https://portal.azure.com) にサインインします。

1. ホーム ページで、 **[リソースの作成]** を選択します。

1. 検索ボックスに「**Batch サービス**」と入力します。 結果から **[Batch サービス]** を選択し、 **[作成]** を選択します。

1. 次の詳細を入力します。

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="[新しい Batch アカウント] 画面のスクリーンショット。":::

    a. **サブスクリプション**:Batch アカウントを作成するサブスクリプション。 サブスクリプションが 1 つのみの場合は、既定でそのサブスクリプションが選択されます。

    b. **[リソース グループ]** :新しい Batch アカウント用の既存のリソース グループを選択します。必要に応じて、新しく作成することもできます。

    c. **アカウント名**:選択する名前は、アカウントが作成される Azure リージョン内で一意である必要があります (下の **[場所]** を参照)。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。

    d. **[場所]** :Batch アカウントを作成する Azure リージョン。 サブスクリプションとリソース グループでサポートされているリージョンのみがオプションとして表示されます。

    e. **ストレージ アカウント**: Batch アカウントに関連付ける省略可能な [Azure Storage アカウント](accounts.md#azure-storage-accounts)。 既存のストレージ アカウントを選択するか、新しいものを作成できます。 最適なパフォーマンスのために汎用 v2 ストレージ アカウントをお勧めします。

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="ストレージ アカウントの作成時のオプションのスクリーンショット。":::

1. 必要に応じて、 **[詳細設定]** を選択して、**ID の種類**、**公衆ネットワーク アクセス**、または **プール割り当てモード** を選択します。 ほとんどのシナリオでは、既定のオプションで問題ありません。

1. **[確認および作成]** 、 **[作成]** の順に選択してアカウントを作成します。

## <a name="view-batch-account-properties"></a>Batch アカウントのプロパティを表示する

アカウントが作成されたら、そのアカウントを選択して設定とプロパティにアクセスします。 左側のメニューを使用すると、アカウントの設定およびプロパティすべてにアクセスできます。

> [!NOTE]
> Batch アカウントの名前は ID であり、変更することはできません。 Batch アカウントの名前を変更する必要がある場合は、アカウントを削除し、目的の名前で新しいアカウントを作成する必要があります。

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Azure portal の Batch アカウント ページのスクリーンショット。":::

[Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) を使用してアプリケーションを開発する場合は、Batch リソースにアクセスするためにアカウントの URL とキーが必要です  (Batch では Azure Active Directory 認証もサポートされます)。Batch アカウント アクセス情報を表示するには、 **[キー]** を選択します。

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Azure portal の Batch アカウントのキーのスクリーンショット。":::

Batch アカウントに関連付けられているストレージ アカウントの名前とキーを表示するには、 **[ストレージ アカウント]** を選択します。

Batch アカウントに適用される [リソース クォータ](batch-quota-limit.md)を表示するには、 **[クォータ]** を選択します。

## <a name="additional-configuration-for-user-subscription-mode"></a>ユーザー サブスクリプション モードのための追加構成

ユーザー サブスクリプション モードで Batch アカウントを作成することを選択した場合は、アカウントを作成する前に別途、次の手順を実行してください。

> [!IMPORTANT]
> ユーザー サブスクリプション モードで Batch アカウントを作成するユーザーには、Batch アカウントが作成されるサブスクリプションにおいて共同作成者または所有者ロールが割り当てられている必要があります。

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch によるサブスクリプションへのアクセスを許可する (1 回限りの操作)

ユーザー サブスクリプション モードで Batch アカウントを初めて作成する場合は、Batch にサブスクリプションを登録する必要があります (既にこれを行っている場合は、次のセクションに進んでください)。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[すべてのサービス]**  >  **[サブスクリプション]** の順に選択し、Batch アカウントに使用するサブスクリプションを選択します。

1. **[サブスクリプション]** ページで、 **[リソース プロバイダー]** を選択し、 **[Microsoft.Batch]** を検索します。 **Microsoft.Batch** リソース プロバイダーがサブスクリプションに登録されていることを確認します。 そうでない場合は、画面の上部近くにある **[登録]** リンクを選択します。

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Microsoft.Batch リソース プロバイダーを示すスクリーンショット。":::

1. **[サブスクリプション]** ページに戻り、 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]**  >  **[追加]**  >  **[ロール割り当ての追加]** を選択します。

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="サブスクリプションの [ロールの割り当て] ページのスクリーンショット。":::

1. **[ロール割り当ての追加]** ページで、 **[共同作成者]** または **[所有者]** ロールを選択し、Batch API を探します。 この API を見つけるには、**Microsoft Azure Batch** または **MicrosoftAzureBatch** を検索します。 (**ddbf3205-c6bd-46ae-8127-60eb93363864** が Batch API のアプリケーション ID です。)

1. Batch API を見つけたら、それを選択して **[保存]** を選択します。

### <a name="create-a-key-vault"></a>Key Vault の作成

ユーザー サブスクリプション モードでは、[Azure キー コンテナー](../key-vault/general/overview.md)が必要です。 キー コンテナーは、作成する Batch アカウントと同じサブスクリプションとリージョンにある必要があります。

1. [Azure portal](https://portal.azure.com) のホーム ページで **[リソースの作成]** を選択します。
1. 検索ボックスに「**Key Vault**」と入力します。 結果から **[Key Vault]** を選択し、 **[作成]** を選択します。
1. **[キー コンテナーの作成]** ページで、キー コンテナーの名前を入力し、Batch アカウント用に必要とするものと同じリージョンで新しいリソース グループを作成します。 残りの設定については既定値のままにして、 **[作成]** を選択します。

ユーザー サブスクリプション モードで Batch アカウントを作成するときは、プール割り当てモードとして **[ユーザー サブスクリプション]** を指定し、作成したキー コンテナーを選択し、Azure Batch にキー コンテナーへのアクセス権を付与するチェック ボックスをオンにします。

キー コンテナーへのアクセス権を手動で付与する場合は、キー コンテナーの **[アクセス ポリシー]** セクションに移動し、 **[アクセス ポリシーの追加]** を選択します。 **[プリンシパルの選択]** の横にあるリンクを選択し、**Microsoft Azure Batch** (アプリケーション ID **ddbf3205-c6bd-46ae-8127-60eb93363864**) を検索します。 そのプリンシパルを選択し、ドロップ ダウン メニューを使用して **[シークレットのアクセス許可]** を構成します。 Azure Batch には、少なくとも **[取得]** 、 **[一覧]** 、 **[設定]** 、および **[削除]** のアクセス許可を付与する必要があります。 [論理的な削除が有効になっているキー コンテナ―](../key-vault/general/soft-delete-overview.md)の場合は、Azure Batch にも **回復** 権限を付与する必要があります。

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Azure Batch に対する [シークレットのアクセス許可] の選択のスクリーンショット":::

**[追加]** を選択し、リンクされている **Key Vault** リソースの **[Azure Virtual Machines (展開用)]** と **[Azure Resource Manager (テンプレートの展開用)]** の各チェック ボックスがオンになっていることを確認します。 **[保存]** を選択して、変更をコミットします。

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="[アクセス ポリシー] 画面のスクリーンショット。":::

### <a name="configure-subscription-quotas"></a>サブスクリプション クォータを構成する

ユーザー サブスクリプションの Batch アカウントの場合は、[コア クォータ](batch-quota-limit.md)を手動で設定する必要があります。 Standard Batch コア クォータはユーザー サブスクリプション モードのアカウントには適用されず、リージョンのコンピューティング コア、シリーズごとのコンピューティング コア、およびその他のリソースに対する[サブスクリプションのクォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)が使用され、適用されます。

1. [Azure portal](https://portal.azure.com) でユーザー サブスクリプション モードの Batch アカウントを選択し、その設定とプロパティを表示します。
1. 左側のメニューの **[クォータ]** を選択して、Batch アカウントに関連付けられているコア クォータを表示および構成します。

## <a name="other-batch-account-management-options"></a>その他の Batch アカウント管理オプション

Azure portal を利用する方法に加えて、次のようなツールを使用して Batch アカウントを作成および管理できます。

- [Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- [Batch .NET クライアント ライブラリ](quick-run-dotnet.md)または [Python](quick-run-python.md) を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。 これらのクイック スタートでは、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行するサンプル アプリケーションについて説明します。ここで、ワークロード ファイルのステージングと取得には、Azure Storage を使用します。