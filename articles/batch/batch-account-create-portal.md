---
title: "Azure Portal で Batch アカウントを作成する | Microsoft Docs"
description: "Azure ポータルで、クラウド内で大規模な並列ワークロードを実行する Azure Batch アカウントを作成する方法について説明します"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 520d1d42d35b25db1a35d4317e9eb616cf5de565
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure Portal で Batch アカウントを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

[Azure Portal][azure_portal] で Azure Batch アカウントを作成し、自分のコンピューティング シナリオに適したアカウント プロパティを選ぶ方法について説明します。 アクセス キーやアカウント URL のような重要なアカウント プロパティを確認できる場所を紹介します。

Batch アカウントとシナリオの背景情報については、[機能の概要](batch-api-basics.md)に関するページを参照してください。



## <a name="create-a-batch-account"></a>Batch アカウントを作成する

ポータルを使用して、2 つの*プール割り当てモード*のどちらかで Batch アカウントを作成します。そのモードとは、**Batch サービス** モードと、それよりも新しい**ユーザー サブスクリプション** モード (より多くの構成が必要) です。 これら 2 つのモードについては、[機能の概要](batch-api-basics.md#account)に関するページを参照してください。 ユーザー サブスクリプション モードの機能については、[こちらのブログ投稿](https://blogs.technet.microsoft.com/windowshpc/2017/03/17/azure-batch-vnet-and-custom-image-support-for-virtual-machine-pools/)も参照してください。

## <a name="batch-service-mode"></a>Batch サービス モード



1. [Azure Portal][azure_portal] にサインインします。
2. **[新規]**  >  **[Compute]**  >  **[Batch サービス]** の順にクリックします。

    ![Marketplace での Batch][marketplace_portal]
3. **[新しい Batch アカウント]** ブレードが表示されます。 各ブレード要素に関する以下の説明を参照してください。

    ![Batch アカウントを作成する][account_portal]

    a. **アカウント名**: 選択する Batch アカウント名は、アカウントが作成される Azure リージョン内で一意である必要があります (以下の「**場所**」を参照してください)。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。

    b. **サブスクリプション**: Batch アカウントを作成するサブスクリプション。 サブスクリプションが 1 つのみの場合は、既定でそのサブスクリプションが選択されます。

    c. **プール割り当てモード**: **[Batch サービス]** を選択します。

    c. **リソース グループ**: 新しい Batch アカウント用の既存のリソース グループを選択します。必要に応じて、新しく作成することもできます。

    d. **場所**: Batch アカウントを作成する Azure リージョン。 サブスクリプションとリソース グループでサポートされているリージョンのみがオプションとして表示されます。

    e. **ストレージ アカウント** (省略可能): Batch アカウントに関連付ける汎用の Azure Storage アカウント。 ほとんどの Batch アカウントでこれをお勧めします。 詳細については、この記事で後ほど説明する「[リンクされた Azure Storage アカウント](#linked-azure-storage-account)」を参照してください。

4. **[作成]** をクリックしてアカウントを作成します。

   ポータルにデプロイが進行中であることが示されます。 完了すると **[通知]** に "**デプロイメントが成功しました**" という通知が表示されます。

## <a name="user-subscription-mode"></a>ユーザー サブスクリプション モード

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch によるサブスクリプションへのアクセスを許可する (1 回限りの操作)
ユーザー サブスクリプション モードで Batch アカウントを初めて作成する場合は、次の手順を実行して Batch にサブスクリプションを登録します  (既に実行済みの場合は、次のセクションに移ってください)。

1. [Azure Portal][azure_portal] にサインインします。

2. **[その他のサービス]** > **[サブスクリプション]** の順にクリックし、Batch アカウントに使用するサブスクリプションをクリックします。

3. **[サブスクリプション]** ブレードで、**[アクセス制御 (IAM)]** > **[追加]** の順にクリックします。

    ![サブスクリプションのアクセスの制御][subscription_access]

4. **[アクセス許可の追加]** ブレードで、**[共同作成者]** ロールを選択し、Batch API を探します。 API が見つかるまで、次の各文字列を検索します。
    1. **MicrosoftAzureBatch**。
    2. **Microsoft Azure Batch**。 新しい Azure AD テナントでは、この名前が使用される場合があります。
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** は Batch API の ID です。 

5. Batch API を見つけたら選択して、**[保存]** をクリックします。

    ![Batch のアクセス許可を追加する][add_permission]

### <a name="create-a-key-vault"></a>Key Vault を作成します
ユーザー サブスクリプション モードでは、作成する Batch アカウントと同じリソース グループに属する Azure キー コンテナーが必要です。 Batch を[利用でき](https://azure.microsoft.com/regions/services/)、お使いのサブスクリプションでサポートされているリージョンにそのリソース グループが属していることを確認してください。

1. [Azure Portal][azure_portal] で、**[新規]** > **[セキュリティ + ID]** > **[Key Vault]** の順にクリックします。

2. **[Key Vault の作成]** ブレードで、キー コンテナーの名前を入力し、Batch アカウント用のリージョンでリソース グループを作成します。 残りの設定については既定値のままにして、**[作成]** をクリックします。

### <a name="create-a-batch-account"></a>Batch アカウントを作成する

1. [Azure Portal][azure_portal] で、**[新規]** > **[Compute]** > **[Batch サービス]** の順にクリックします。

    ![Marketplace での Batch][marketplace_portal]
3. **[新しい Batch アカウント]** ブレードが表示されます。 各ブレード要素に関する以下の説明を参照してください。

    ![Batch アカウントを作成する][account_portal_byos]

    a. **アカウント名**: 選択する Batch アカウント名は、アカウントが作成される Azure リージョン内で一意である必要があります (以下の「**場所**」を参照してください)。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。

    b. **サブスクリプション**: 複数のサブスクリプションがある場合は、Batch サービスに登録したサブスクリプションを選択します。

    c. **プール割り当てモード**: **[ユーザー サブスクリプション]** を選択します。

    d. **キー コンテナー**: 前のセクションで Batch アカウント用に作成したキー コンテナーを選択します。 必要に応じて、新しいキー コンテナーを作成します。 コンテナーを選択した後で、チェック ボックスをオンにして Azure Batch によるそのキー コンテナーへのアクセスを許可します。

    c. **リソース グループ**: キー コンテナーを作成したリソース グループを選択します。

    d. **場所**: Batch アカウント用のキー コンテナーを作成した Azure リージョンです。

    e. **ストレージ アカウント** (省略可能): Batch アカウントに関連付ける汎用の Azure Storage アカウント。 ほとんどの Batch アカウントでこれをお勧めします。 詳細については、下の「[リンクされた Azure Storage アカウント](#linked-azure-storage-account)」を参照してください。

4. **[作成]** をクリックしてアカウントを作成します。

   ポータルにデプロイが進行中であることが示されます。 完了すると **[通知]** に "**デプロイメントが成功しました**" という通知が表示されます。



## <a name="view-batch-account-properties"></a>Batch アカウントのプロパティを表示する
アカウントが作成されたら、 **[Batch アカウント]** ブレードを開いて Batch アカウントの設定とプロパティにアクセスできます。 [Batch アカウント] ブレードの左側のメニューから、アカウントのすべての設定とプロパティにアクセスできます。

![Batch account blade in Azure portal][account_blade]

* **Batch アカウント URL**: アプリケーションを [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) を使用して開発する場合は、Batch リソースにアクセスするためにアカウント URL が必要になります。 Batch アカウント URL の形式を次に示します。

    `https://<account_name>.<region>.batch.azure.com`

![Batch account URL in portal][account_url]

* **アクセス キー** (Batch サービス モード): アプリケーションから Batch アカウントへのアクセスを認証するには、アカウント アクセス キーが必要になります  (この設定は、Azure Active Directory 認証を使用するユーザー サブスクリプション モードでは利用できません)。

    Batch アカウントのアクセス キーを表示または再生成するには、[Batch アカウント] ブレードの左側メニューにある **[検索]** ボックスに「`keys`」と入力し、**[キー]** を選択します。

    ![Batch account keys in Azure portal][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>リンクされた Azure Storage アカウント

必要に応じて汎用の Azure Storage アカウントを Batch アカウントにリンクすることができます。 Batch の[アプリケーション パッケージ](batch-application-packages.md)機能は、[Batch File Conventions .NET](batch-task-output.md) ライブラリと同様に、Azure Blob Storage を使用します。 これらのオプション機能は、Batch タスクで実行するアプリケーションのデプロイや、そのアプリケーションによって生成されるデータの保持に役立ちます。

Batch アカウント専用として使用する新しいストレージ アカウントを作成することをお勧めします。

![汎用ストレージ アカウントの作成][storage_account]

> [!NOTE]
> Azure Batch で現在サポートされているのは、汎用的なストレージ アカウントの種類のみです。 このアカウントの種類については、「[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)」の「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」の手順 5. で説明されています。
>
>

> [!WARNING]
> リンクされた Storage アカウントのアクセス キーを再生成する際は次の点に注意してください。 Storage アカウントのキーは 1 つだけ再生成し、リンクされた Storage アカウントのブレードにある **[キーの同期]** をクリックします。 キーがプール内のコンピューティング ノードに反映されるまで 5 分待ってから、必要に応じて他のキーの再生成と同期を行います。 両方のキーを同時に再生成すると、コンピューティング ノードはどちらのキーも同期できず、Storage アカウントにアクセスできなくなります。
>
>

![ストレージ アカウント キーの再生成][4]

## <a name="batch-service-quotas-and-limits"></a>Batch サービスのクォータと制限
Azure サブスクリプションやその他の Azure サービスと同様に、Batch アカウントにも特定の [クォータと制限](batch-quota-limit.md) が適用される点に注意してください。 Batch アカウントの現在のクォータは、ポータル内のアカウントの **[プロパティ]**に表示されます。

![Batch account quotas in Azure portal][quotas]



また、これらのクォータの多くは、Azure Portal で無料の製品サポート要求を送信するだけで増やすことができます。 クォータ引き上げ要求の詳細については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md) 」を参照してください。

## <a name="other-batch-account-management-options"></a>その他の Batch アカウント管理オプション
Azure Portal を利用する方法に加えて、次に示す方法でも Batch アカウントを作成および管理できます。

* [Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>次のステップ
* Batch サービスの概念と機能の詳細については、[Batch 機能の概要](batch-api-basics.md)に関するページを参照してください。 この記事では、プール、コンピューティング ノード、ジョブ、タスクなど、主要な Batch リソースについて説明すると共に、大規模なコンピューティング ワークロードを実行する機能の概要について説明しています。
* [Batch .NET クライアント ライブラリ](batch-dotnet-get-started.md)または [Python](batch-python-tutorial.md) を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。 これらの入門記事では、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行する実用アプリケーションの開発手順を説明しています。また、Azure Storage を使用してワークロード ファイルのステージングと取得を行う方法についても取り上げています。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "ストレージ アカウント キーの再生成"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png

