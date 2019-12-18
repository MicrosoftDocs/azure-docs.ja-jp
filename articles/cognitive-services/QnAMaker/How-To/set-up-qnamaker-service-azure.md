---
title: QnA Maker サービスを設定する - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベースを作成する前に、まず Azure で QnA Maker サービスをセットアップしておく必要があります。 QnA Maker サービスは、サブスクリプション内で新しいリソースを作成することが認可されているユーザーであれば、だれでもセットアップできます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6d52062561e3f08a214f3e191706583edc844786
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794208"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker のリソースを管理する

QnA Maker のナレッジ ベースを作成する前に、まず Azure で QnA Maker サービスをセットアップしておく必要があります。 QnA Maker サービスは、サブスクリプション内で新しいリソースを作成することが認可されているユーザーであれば、だれでもセットアップできます。

## <a name="types-of-keys-in-qna-maker"></a>QnA Maker でのキーの種類

QnA Maker サービスでは、**サブスクリプション キー**と**エンドポイント キー**の 2 種類のキーを扱います。

![キー管理](../media/qnamaker-how-to-key-management/key-management.png)

|名前|Location|目的|
|--|--|--|
|Subscription key|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|これらのキーは、[QnA Maker 管理サービス API](https://go.microsoft.com/fwlink/?linkid=2092179) へのアクセスに使用されます。 これらの API を使用して、ナレッジ ベース内の質問と回答を編集したり、ナレッジ ベースを公開したりできます。 これらのキーは、新しい QnA Maker サービスを作成するときに作成されます。<br><br>これらのキーは、 **[キー]** ページの **Cognitive Services** リソースで確認できます。|
|エンドポイント キー|[QnA Maker ポータル](http://www.qnamaker.ai)|これらのキーは、公開されたナレッジ ベースのエンドポイントにアクセスして、ユーザーの質問に対する回答を取得する目的で使用されます。 通常、このエンドポイントは、QnA Maker サービスに接続するチャット ボットまたはクライアント アプリケーション コードで使用されます。 これらのキーは、QnA Maker ナレッジ ベースを公開するときに作成されます。<br><br>これらのキーは、 **[サービスの設定]** ページで確認できます。 このページは、ページの右上にあるドロップダウン メニュー内のユーザーのメニューに含まれています。|

## <a name="create-a-new-qna-maker-service"></a>新しい QnA Maker サービスの作成

この手順では、ナレッジ ベースのコンテンツを管理するために必要な Azure リソースを作成します。 これらの手順を完了すると、Azure portal のリソース用の **[キー]** ページに、_サブスクリプション_ キーが表示されます。

1. Azure portal にサインインし、[QnA Maker リソースを作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)します。

1. 使用条件を読んだ後、 **[作成]** を選択します。

    ![新しい QnA Maker サービスの作成](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker** で、適切なレベルとリージョンを選択します。

    ![新しい QnA Maker サービスを作成する - 価格レベルとリージョン](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * **[名前]** フィールドに、この QnA Maker サービスを識別する一意の名前を入力します。 ナレッジ ベースが関連付けられる QnA Maker のエンドポイントも、この名前によって識別されます。
    * QnA Maker リソースのデプロイ先となる**サブスクリプション**を選択します。
    * QnA Maker 管理サービス (ポータルおよび Management API) の**価格レベル**を選択します。 [詳細については、SKU の価格](https://aka.ms/qnamaker-pricing)に関するページをご覧ください。
    * この QnA Maker リソースのデプロイ先として、新しい**リソース グループ**を作成する (推奨) か、既存のリソース グループを使用します。 QnA Maker はいくつかの Azure リソースを作成します。 これらのリソースを保持するリソース グループを作成すると、リソースの検索、管理、および削除を、リソース グループ名によって簡単に実行できます。
    * **リソース グループの場所**を選択します。
    * Azure Cognitive Search サービスの**検索価格レベル**を選択します。 Free レベル オプションを利用できない (灰色表示されている) 場合、それは、ご利用のサブスクリプションで Free サービスが既にデプロイされていることを意味します。 その場合は、Basic レベルから開始する必要があります。 [Azure Cognitive Search の価格の詳細](https://azure.microsoft.com/pricing/details/search/)に関するページを参照してください。
    * Azure Cognitive Search インデックスのデプロイ先となる**検索場所**を選択します。 顧客データをどの場所に格納する必要があるかの制限に基づいて、Azure Cognitive Search で使用する場所を選択できます。
    * **[アプリ名]** フィールドに、Azure App Service インスタンスの名前を入力します。
    * App Service の既定のレベルは Standard (S1) になります。 このプランは、作成後に変更することができます。 詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」をご覧ください。
    * App Service のデプロイ先となる **Web サイトの場所**を選択します。

        > [!NOTE]
        > **検索場所**は **Web サイトの場所**と異なっていてもかまいません。

    * **Application Insights** を有効にするかどうかを選択します。 **Application Insights** を有効にした場合、トラフィック、チャットのログ、エラーに関するテレメトリが QnA Maker によって収集されます。
    * Application Insights リソースのデプロイ先となる **App Insights の場所**を選択します。
    * コスト削減対策として、QnA Maker 用に作成する Azure リソースのすべてではなく、一部を[共有](#share-existing-services-with-qna-maker)することができます。 

1. すべてのフィールドを確認した後、 **[作成]** を選択します。 プロセスが完了するまでに数分かかることがあります。

1. デプロイが完了すると、サブスクリプション内に次のリソースが作成されていることがわかります。

   ![リソースによって新しい QnA Maker サービスが作成された](../media/qnamaker-how-to-setup-service/resources-created.png)

    種類が _Cognitive Services_ であるリソースには、_サブスクリプション_ キーがあります。

## <a name="find-subscription-keys-in-the-azure-portal"></a>Azure portal でサブスクリプション キーを確認する

QnA Maker リソースを作成した Azure portal で、サブスクリプション キーの表示とリセットを実行できます。

1. Azure portal で QnA Maker リソースに移動し、種類が _Cognitive Services_ であるリソースを選択します。

    ![QnA Maker のリソース リスト](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **[キー]** に移動します。

    ![Subscription key](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>QnA Maker ポータルでエンドポイント キーを確認する

ナレッジ ベースの呼び出しにはエンドポイント キーが使用されるため、エンドポイントはリソースと同じリージョンにあります。

エンドポイント キーは、[QnA Maker ポータル](https://qnamaker.ai)から管理することができます。

1. [QnA Maker ポータル](https://qnamaker.ai)にサインインし、お使いのプロファイルに移動してから、 **[サービスの設定]** を選択します。

    ![エンドポイント キー](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. キーを表示またはリセットします。

    ![エンドポイント キー マネージャー](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >キーが漏えいしていると思われる場合は、キーを更新してください。 それに合わせて、クライアント アプリまたはボットのコードの変更が必要になることがあります。

## <a name="share-existing-services-with-qna-maker"></a>既存のサービスと QnA Maker の共有

QnA Maker はいくつかの Azure リソースを作成します。 管理を軽減し、コスト共有のメリットを得るために、次の表を使用して、共有できるものとできないものを理解してください。

|Service|共有|理由|
|--|--|--|
|Cognitive Services|X|仕様により不可能|
|App Service プラン|✔|App Service プランに対して固定のディスク領域が割り当てられています。 同じ App Service プランを共有している他のアプリによって大量のディスク領域が使用された場合、QnA Maker App Service インスタンスで問題が発生します。|
|App Service|X|仕様により不可能|
|Application Insights|✔|共有できます|
|検索サービス|✔|1. `testkb` は QnA Maker サービス用の予約された名前です。他のサービスで使用することはできません。<br>2.`synonym-map` という名前のシノニム マップは、QnA Maker サービス用に予約されています。<br>3.公開されるナレッジ ベースの数は、Search サービスのレベルによって制限されます。 使用可能な空きインデックスがある場合は、他のサービスでそれらを使用できます。|

[App Service](../../../app-service/index.yml) と [Search Service](../../../search/index.yml) の詳細をご覧ください。

### <a name="using-a-single-search-service"></a>単一の Search サービスの使用

ポータルを使用して QnA サービスとその依存関係 (Search など) を作成すると、Search サービスが自動的に作成され、QnA Maker サービスにリンクされます。 これらのリソースが作成された後、既存の Search サービスを使用するように App Service 設定を更新して、先ほど作成されたサービスを削除できます。

Azure Resource Manager テンプレートを使用して QnA サービスを作成する場合は、すべてのリソースを作成し、既存の Search サービスを使用するように App Service の作成を制御できます。

## <a name="upgrade-qna-maker"></a>QnA Maker をアップグレードする

|アップグレード|理由|
|--|--|
|QnA Maker 管理 SKU の[アップグレード](#upgrade-qna-maker-sku)|ナレッジ ベースにより多くの質問と回答を含めたい。|
|App Service SKU の[アップグレード](#upgrade-app-service)|ナレッジ ベースでクライアント アプリ (チャット ボットなど) からのより多くの要求に対応する必要がある。|
|Azure Cognitive Search サービスの[アップグレード](#upgrade-the-azure-cognitive-search-service)|多数のナレッジ ベースを使用する予定である。|


### <a name="upgrade-qna-maker-sku"></a>QnA Maker の SKU をアップグレードする

現在のレベルよりも多くの質問と回答をナレッジ ベースに含めたい場合は、QnA Maker サービスの価格レベルをアップグレードします。

QnA Maker 管理 SKU をアップグレードするには、次の手順に従います。

1. Azure portal の QnA Maker リソースに移動し、 **[価格レベル]** を選択します。

    ![QnA Maker リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 適切な SKU を選択し、 **[選択]** を押します。

    ![QnA Maker の価格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>App Service をアップグレードする

 ナレッジ ベースでクライアント アプリからのより多くの要求に対応する必要がある場合は、App Service の価格レベルをアップグレードします。

App Service は、[スケールアップ](https://docs.microsoft.com/azure/app-service/manage-scale-up)またはスケールアウトすることができます。

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

## <a name="get-the-latest-runtime-updates"></a>最新のランタイム更新プログラムを取得する

QnA Maker ランタイムは、Azure portal で [QnA Maker サービスを作成する](./set-up-qnamaker-service-azure.md)ときにデプロイされる Azure App Service インスタンスの一部です。 ランタイムの更新は定期的に行われます。 QnA Maker App Service インスタンスは、2019 年 4 月のサイト拡張リリース (バージョン 5+) 以降は自動更新モードになります。 この更新は、アップグレード中のダウンタイムがゼロになるように設計されています。

[https://www.qnamaker.ai/UserSettings](https://www.qnamaker.ai/UserSettings ) で現在のバージョンを確認できます。 ご使用のバージョンがバージョン 5.x よりも古い場合は、App Service を再起動して最新の更新プログラムを適用する必要があります。

1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    ![QnA Maker Azure リソース グループ](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service インスタンスをクリックして、 **[概要]** セクションを開きます。

    ![QnA Maker App Service インスタンス](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. App Service を再起動します。 更新プロセスは数秒で完了します。 この再起動中、エンド ユーザーは、この QnA Maker サービスを使用する依存アプリケーションやボットを使用できなくなります。

    ![QnA Maker App Service インスタンスの再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>管理サービスのリージョン

QnA Maker の管理サービスは、QnA Maker ポータルと初期データ処理でのみ使用されます。 このサービスは、米国西部リージョンでのみ利用できます。 この米国西部のサービスでは、顧客データは保存されません。

## <a name="next-steps"></a>次の手順

[App Service](../../../app-service/index.yml) と [Search Service](../../../search/index.yml) の詳細をご覧ください。

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成と発行](../Quickstarts/create-publish-knowledge-base.md)
