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
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 09be891b5385871554f45bc1f824b4351ffd3bc2
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure Portal で Batch アカウントを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

[Azure Portal][azure_portal] で Azure Batch アカウントを作成する方法と、アクセス キーやアカウント URL のような重要なアカウント プロパティを確認できる場所について説明します。 また、Batch の価格設定や、Azure Storage アカウントを Batch アカウントにリンクして[アプリケーション パッケージ](batch-application-packages.md)の使用と[ジョブおよびタスク出力の保持](batch-task-output.md)を可能にする方法についても紹介します。

## <a name="create-a-batch-account"></a>Batch アカウントを作成する
1. [Azure Portal][azure_portal] にサインインします。
2. **[新規]**  >  **[Compute]**  >  **[Batch サービス]** の順にクリックします。
   
    ![Marketplace での Batch][marketplace_portal]
3. **[新しい Batch アカウント]** ブレードが表示されます。 ブレードの各要素の説明については、下記の *a* ～ *e* の項目を参照してください。
   
    ![Batch アカウントを作成する][account_portal]
   
    a. **アカウント名**: Batch アカウントの名前。 選択する名前は、新しいアカウントが作成される Azure リージョン内で一意である必要があります (以下の「**場所**」を参照してください)。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。
   
    b. **サブスクリプション**: Batch アカウントを作成するサブスクリプション。 サブスクリプションが&1; つのみの場合は、既定でそのサブスクリプションが選択されます。
   
    c. **リソース グループ**: 新しい Batch アカウント用の既存のリソース グループを選択します。必要に応じて、新しく作成することもできます。
   
    d. **場所**: Batch アカウントを作成する Azure リージョン。 サブスクリプションとリソース グループでサポートされているリージョンのみがオプションとして表示されます。
   
    e. **ストレージ アカウント** (省略可能): 新しい Batch アカウントに関連付ける汎用の Azure ストレージ アカウント。 詳細については、下の「[リンクされた Azure Storage アカウント](#linked-azure-storage-account)」を参照してください。

4. **[作成]** をクリックしてアカウントを作成します。
   
   アカウントを**デプロイ中**であることがポータルに示され、完了すると *[通知]* に "**デプロイが成功しました**" という通知が表示されます。

## <a name="view-batch-account-properties"></a>Batch アカウントのプロパティを表示する
アカウントが作成されたら、 **[Batch アカウント]** ブレードを開いて Batch アカウントの設定とプロパティにアクセスできます。 [Batch アカウント] ブレードの左側のメニューから、アカウントのすべての設定とプロパティにアクセスできます。

![Batch account blade in Azure portal][account_blade]

* **Batch アカウント URL**: アプリケーションを [Batch API](batch-apis-tools.md#batch-development-apis) を使用して開発する場合は、Batch リソースにアクセスするためにアカウント URL が必要になります。 Batch アカウント URL の形式を次に示します。
  
    `https://<account_name>.<region>.batch.azure.com`

![Batch account URL in portal][account_url]

* **アクセス キー**: アプリケーションから Batch アカウントへのアクセスを認証するには、アカウント アクセス キーが必要になります。 Batch アカウントのアクセス キーを表示または再生成するには、[Batch アカウント] ブレードの左側メニューにある **[検索]** ボックスに「`keys`」と入力し、**[キー]** を選択します。
  
    ![Batch account keys in Azure portal][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>リンクされた Azure Storage アカウント

既に述べたように、必要に応じて汎用の Azure Storage アカウントを Batch アカウントにリンクすることができます。 Batch の[アプリケーション パッケージ](batch-application-packages.md)機能は、[Batch File Conventions .NET](batch-task-output.md) ライブラリと同様に、Azure Blob Storage を使用します。 これらのオプション機能は、Batch タスクで実行するアプリケーションのデプロイや、そのアプリケーションによって生成されるデータの保持に役立ちます。

Batch アカウント専用として使用する新しいストレージ アカウントを作成することをお勧めします。

![Creating a "General purpose" storage account][storage_account]

> [!NOTE] 
> Azure Batch で現在サポートされているのは、汎用的なストレージ アカウントの種類のみです。 このアカウントの種類については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」の「[ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」の手順 5. で説明されています。
>
>

> [!WARNING]
> リンクされた Storage アカウントのアクセス キーを再生成する際は次の点に注意してください。 Storage アカウントのキーは&1; つだけ再生成し、リンクされた Storage アカウントのブレードにある **[キーの同期]** をクリックします。 キーがプール内のコンピューティング ノードに反映されるまで&5; 分待ってから、必要に応じて他のキーの再生成と同期を行います。 両方のキーを同時に再生成すると、コンピューティング ノードはどちらのキーも同期できず、Storage アカウントにアクセスできなくなります。
> 
> 

![ストレージ アカウント キーの再生成][4]

## <a name="batch-service-quotas-and-limits"></a>Batch サービスのクォータと制限
Azure サブスクリプションやその他の Azure サービスと同様に、Batch アカウントにも特定の [クォータと制限](batch-quota-limit.md) が適用される点に注意してください。 Batch アカウントの現在のクォータは、ポータル内のアカウントの **[プロパティ]**に表示されます。

![Batch account quotas in Azure portal][quotas]

Batch ワークロードの設計やスケールアップを行う際は、これらのクォータに留意してください。 たとえば、プールのコンピューティング ノード数がターゲットとして指定した数に満たない場合は、Batch アカウントのコア クォータ制限に達している可能性があります。

Batch アカウントのクォータは、リージョンごと、サブスクリプションごとであるため、既定では複数の Batch アカウントを持つことができます (それぞれが別のリージョンにある場合)。 1 つの Batch アカウントで複数の Batch ワークロードを実行することも、同じサブスクリプションで異なる Azure リージョンの複数の Batch アカウントにワークロードを分散することもできます。

また、これらのクォータの多くは、Azure Portal で無料の製品サポート要求を送信するだけで増やすことができます。 クォータ引き上げ要求の詳細については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md) 」を参照してください。

## <a name="other-batch-account-management-options"></a>その他の Batch アカウント管理オプション
Azure Portal を利用する方法に加えて、次に示す方法でも Batch アカウントを作成および管理できます。

* [Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](../cli-install-nodejs.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>次のステップ
* Batch サービスの概念と機能の詳細については、 [Azure Batch 機能の概要](batch-api-basics.md) に関するページを参照してください。 この記事では、プール、コンピューティング ノード、ジョブ、タスクなど、主要な Batch リソースについて説明すると共に、大規模なコンピューティング ワークロードを実行する機能の概要について説明しています。
* [Batch .NET クライアント ライブラリ](batch-dotnet-get-started.md)を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。 この[入門記事](batch-dotnet-get-started.md)では、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行する実用アプリケーションの開発手順を説明しています。また、Azure Storage を使用してワークロード ファイルのステージングと取得を行う方法についても取り上げています。

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

