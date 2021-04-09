---
title: QnA Maker サービスを設定する - QnA Maker
description: QnA Maker のナレッジ ベースを作成する前に、まず Azure で QnA Maker サービスをセットアップしておく必要があります。 QnA Maker サービスは、サブスクリプション内で新しいリソースを作成することが認可されているユーザーであれば、だれでもセットアップできます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102219269"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker のリソースを管理する

QnA Maker のナレッジ ベースを作成する前に、まず Azure で QnA Maker サービスをセットアップしておく必要があります。 QnA Maker サービスは、サブスクリプション内で新しいリソースを作成することが認可されているユーザーであれば、だれでもセットアップできます。

リソースを作成する前に、次の概念について十分に理解しておくことをお勧めします。

* "[QnA Maker のリソース](../Concepts/azure-resources.md)"
* [キーの作成と発行](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>新しい QnA Maker サービスの作成

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

この手順では、ナレッジ ベースのコンテンツを管理するために必要な Azure リソースを作成します。 これらの手順を完了すると、Azure portal のリソース用の **[キー]** ページに、_サブスクリプション_ キーが表示されます。

1. Azure portal にサインインし、[QnA Maker リソースを作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)します。

1. 使用条件を読んだ後、 **[作成]** を選択します。

    ![新しい QnA Maker サービスの作成](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker** で、適切なレベルとリージョンを選択します。

    ![新しい QnA Maker サービスを作成する - 価格レベルとリージョン](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * **[名前]** フィールドに、この QnA Maker サービスを識別する一意の名前を入力します。 ナレッジ ベースが関連付けられる QnA Maker のエンドポイントも、この名前によって識別されます。
    * QnA Maker リソースのデプロイ先となる **サブスクリプション** を選択します。
    * QnA Maker 管理サービス (ポータルおよび Management API) の **価格レベル** を選択します。 [詳細については、SKU の価格](https://aka.ms/qnamaker-pricing)に関するページをご覧ください。
    * この QnA Maker リソースのデプロイ先として、新しい **リソース グループ** を作成する (推奨) か、既存のリソース グループを使用します。 QnA Maker はいくつかの Azure リソースを作成します。 これらのリソースを保持するリソース グループを作成すると、リソースの検索、管理、および削除を、リソース グループ名によって簡単に実行できます。
    * **リソース グループの場所** を選択します。
    * Azure Cognitive Search サービスの **検索価格レベル** を選択します。 Free レベル オプションを利用できない (灰色表示されている) 場合、それは、ご利用のサブスクリプションで Free サービスが既にデプロイされていることを意味します。 その場合は、Basic レベルから開始する必要があります。 [Azure Cognitive Search の価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページを参照してください。
    * Azure Cognitive Search インデックスのデプロイ先となる **検索場所** を選択します。 顧客データをどの場所に格納する必要があるかの制限に基づいて、Azure Cognitive Search で使用する場所を選択できます。
    * **[アプリ名]** フィールドに、Azure App Service インスタンスの名前を入力します。
    * App Service の既定のレベルは Standard (S1) になります。 このプランは、作成後に変更することができます。 詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」をご覧ください。
    * App Service のデプロイ先となる **Web サイトの場所** を選択します。

        > [!NOTE]
        > **検索場所** は **Web サイトの場所** と異なっていてもかまいません。

    * **Application Insights** を有効にするかどうかを選択します。 **Application Insights** を有効にした場合、トラフィック、チャットのログ、エラーに関するテレメトリが QnA Maker によって収集されます。
    * Application Insights リソースのデプロイ先となる **App Insights の場所** を選択します。
    * コスト削減対策として、QnA Maker 用に作成する Azure リソースのすべてではなく、一部を[共有](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)することができます。

1. すべてのフィールドを確認した後、 **[作成]** を選択します。 プロセスが完了するまでに数分かかることがあります。

1. デプロイが完了すると、サブスクリプション内に次のリソースが作成されていることがわかります。

   ![リソースによって新しい QnA Maker サービスが作成された](../media/qnamaker-how-to-setup-service/resources-created.png)

    種類が _Cognitive Services_ であるリソースには、_サブスクリプション_ キーがあります。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

この手順では、ナレッジ ベースのコンテンツを管理するために必要な Azure リソースを作成します。 これらの手順を完了すると、Azure portal のリソース用の **[キー]** ページに、*サブスクリプション* キーが表示されます。

1. Azure portal にサインインし、[QnA Maker リソースを作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)します。

1. 使用条件を読んだ後、 **[作成]** を選択します。

    ![新しい QnA Maker サービスの作成](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker** で、[マネージド (プレビュー)] チェックボックスをオンにして、適切な層とリージョンを選択します。

    ![新しい QnA Maker マネージド サービスを作成する - 価格レベルとリージョン](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * QnA Maker リソースのデプロイ先となる **サブスクリプション** を選択します。
    * この QnA Maker マネージド (プレビュー) リソースのデプロイ先として、新しい **リソース グループ** を作成する (推奨) か、既存のものを使用します。 QnA Maker マネージド (プレビュー) によって、いくつかの Azure リソースが作成されます。 これらのリソースを保持するリソース グループを作成すると、リソースの検索、管理、および削除を、リソース グループ名によって簡単に実行できます。
    * **[名前]** フィールドに、この QnA Maker マネージド (プレビュー) サービスを識別する一意の名前を入力します。 
    * QnA Maker マネージド (プレビュー) サービスをデプロイする **場所** を選択します。 管理 API とサービス エンドポイントは、この場所でホストされます。 
    * QnA Maker マネージド (プレビュー) サービス (プレビュー) の **[価格レベル]** を選択します (プレビューの場合は Free)。 [詳細については、SKU の価格](https://aka.ms/qnamaker-pricing)に関するページをご覧ください。
    * Azure Cognitive Search インデックスのデプロイ先となる **検索場所** を選択します。 顧客データをどの場所に格納する必要があるかの制限に基づいて、Azure Cognitive Search で使用する場所を選択できます。
    * Azure Cognitive Search サービスの **検索価格レベル** を選択します。 Free レベル オプションを利用できない (灰色表示されている) 場合、それは、ご利用のサブスクリプションで Free サービスが既にデプロイされていることを意味します。 その場合は、Basic レベルから開始する必要があります。 [Azure Cognitive Search の価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページを参照してください。

1. すべてのフィールドを確認した後、 **[確認と作成]** を選択します。 プロセスが完了するまでに数分かかることがあります。

1. デプロイが完了すると、サブスクリプション内に次のリソースが作成されていることがわかります。

    ![新しい QnA Maker マネージド (プレビュー) サービスによって作成されたリソース](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    種類が _Cognitive Services_ であるリソースには、_サブスクリプション_ キーがあります。

---

## <a name="upgrade-azure-resources"></a>Azure リソースのアップグレード

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>QnA Maker の SKU をアップグレードする

現在のレベルよりも多くの質問と回答をナレッジ ベースに含めたい場合は、QnA Maker サービスの価格レベルをアップグレードします。

QnA Maker 管理 SKU をアップグレードするには、次の手順に従います。

1. Azure portal の QnA Maker リソースに移動し、 **[価格レベル]** を選択します。

    ![QnA Maker リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 適切な SKU を選択し、 **[選択]** を押します。

    ![QnA Maker の価格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>App Service をアップグレードする

ナレッジ ベースでクライアント アプリからのより多くの要求に対応する必要がある場合は、App Service の価格レベルをアップグレードします。

App Service は、[スケールアップ](../../../app-service/manage-scale-up.md)またはスケールアウトすることができます。

Azure portal で App Service リソースに移動し、必要に応じて **[スケールアップ]** と **[スケールアウト]** のどちらかのオプションを選択します。

![QnA Maker App Service のスケーリング](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Azure Cognitive Search サービスのアップグレード

多数のナレッジ ベースを使用する予定の場合は、Azure Cognitive Search サービスの価格レベルをアップグレードします。

現在、Azure Search SKU のインプレース アップグレードを実行することはできません。 ただし、必要な SKU で新しい Azure Search リソースを作成し、その新しいリソースにデータを復元して、QnA Maker スタックにリンクすることはできます。 そのためには、次の手順に従います。

1. Azure portal で新しい Azure Search リソースを作成し、必要な SKU を選択します。

    ![QnA Maker Azure Search リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 元の Azure Search リソースのインデックスを新しいリソースに復元します。 [バックアップと復元のサンプル コード](https://github.com/pchoudhari/QnAMakerBackupRestore)を参照してください。

1. データが復元されたら、新しい Azure Search リソースに移動し、 **[キー]** を選択して、 **[名前]** と **[管理者キー]** を書き留めます。

    ![QnA Maker Azure Search キー](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 新しい Azure Search リソースを QnA Maker スタックにリンクするために、QnA Maker App Service インスタンスに移動します。

    ![QnA Maker App Service インスタンス](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. **[アプリケーションの設定]** を選択し、 **[AzureSearchName]** フィールドと **[AzureSearchAdminKey]** フィールドを手順 3 で書き留めた値に変更します。

    ![QnA Maker App Service の設定](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. App Service インスタンスを再起動します。

    ![QnA Maker App Service インスタンスの再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>無料 Search リソースのアイドル時間ポリシー

QnA Maker リソースを使用していない場合、そのリソースをすべて削除してください。 不使用のリソースを削除しなかった場合、無料の Search リソースを作成したときにナレッジ ベースが機能しなくなります。

無料の Search リソースは、API 呼び出しを受信しない状態で 90 日経過すると削除されます。
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Azure Cognitive Search サービスのアップグレード

多数のナレッジ ベースを使用する予定の場合は、Azure Cognitive Search サービスの価格レベルをアップグレードします。

現在、Azure Search SKU のインプレース アップグレードを実行することはできません。 ただし、必要な SKU で新しい Azure Search リソースを作成し、その新しいリソースにデータを復元して、QnA Maker スタックにリンクすることはできます。 そのためには、次の手順に従います。

1. Azure portal で新しい Azure Search リソースを作成し、必要な SKU を選択します。

    ![QnA Maker Azure Search リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 元の Azure Search リソースのインデックスを新しいリソースに復元します。 [バックアップと復元のサンプル コード](https://github.com/pchoudhari/QnAMakerBackupRestore)を参照してください。

1. 新しい Azure Search リソースを QnA Maker マネージド (プレビュー) サービスにリンクするには、次のトピックを参照してください。

### <a name="inactivity-policy-for-free-search-resources"></a>無料 Search リソースのアイドル時間ポリシー

QnA Maker リソースを使用していない場合、そのリソースをすべて削除してください。 不使用のリソースを削除しなかった場合、無料の Search リソースを作成したときにナレッジ ベースが機能しなくなります。

無料の Search リソースは、API 呼び出しを受信しない状態で 90 日経過すると削除されます。

---

## <a name="delete-azure-resources"></a>Azure リソースを削除する

QnA Maker ナレッジ ベースに使用されているいずれかの Azure リソースを削除すると、ナレッジ ベースは機能しなくなります。 いずれかのリソースを削除する前に、 **[設定]** ページから確実にナレッジ ベースをエクスポートしてください。

## <a name="next-steps"></a>次のステップ

[App Service](../../../app-service/index.yml) と [Search Service](../../../search/index.yml) の詳細をご覧ください。

> [!div class="nextstepaction"]
> [他のユーザーとの作成方法の詳細をご覧ください](../index.yml)
