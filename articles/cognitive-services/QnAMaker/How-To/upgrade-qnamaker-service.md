---
title: QnA Maker サービスのアップグレード - QnA Maker
titleSuffix: Azure Cognitive Services
description: リソースの管理を強化するために、QnA Maker サービスを共有するかアップグレードします。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: 2fdbb245f838d92e84d1247faa610a2f1a66c532
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439765"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>QnA Maker サービスの共有またはアップグレード
リソースの管理を強化するために、QnA Maker サービスを共有するかアップグレードします。 

QnA Maker スタックは、その初回作成後に、個々のコンポーネントを必要に応じてアップグレードすることができます。 依存コンポーネントと SKU 選択の詳細については、[こちら](https://aka.ms/qnamaker-docs-capacity)を参照してください。

## <a name="share-existing-services-with-qna-maker"></a>既存のサービスと QnA Maker の共有

QnA Maker はいくつかの Azure リソースを作成します。 管理を軽減し、コスト共有のメリットを得るため、次の表を使用して、共有できるものとできないものを理解します。

|Service|共有|
|--|--|
|Cognitive Services|X|
|App Service プラン|✔|
|App Service|X|
|Application Insights|✔|
|検索サービス|✔|

## <a name="upgrade-qna-maker-management-sku"></a>QnA Maker 管理 SKU のアップグレード

現在のレベルよりも多くの質問と回答をナレッジ ベースに含める必要がある場合は、QnA Maker サービスの価格レベルをアップグレードします。 

QnA Maker 管理 SKU をアップグレードするには、次の手順に従います。

1. Azure portal の QnA Maker リソースに移動し、 **[価格レベル]** を選択します。

    ![QnA Maker リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. 適切な SKU を選択し、 **[選択]** を押します。

    ![QnA Maker の価格](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>App Service のアップグレード

 ナレッジ ベースでクライアント アプリの要求をより多く提供する必要がある場合は、アプリ サービスの価格レベルをアップグレードします。

App Service は、[スケールアップ](https://docs.microsoft.com/azure/app-service/web-sites-scale)またはスケールダウンすることができます。

1. Azure portal で App Service リソースに移動し、必要に応じて **[スケールアップ]** と **[スケールダウン]** のどちらかのオプションを選択します。

    ![QnA Maker App Service のスケーリング](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Azure Search サービスのアップグレード

多数のナレッジ ベースを使用する予定の場合は、Azure Search サービスの価格レベルをアップグレードします。 

現在、Azure Search SKU のインプレース アップグレードを実行することはできません。 ただし、必要な SKU で新しい Azure Search リソースを作成し、その新しいリソースにデータを復元して、QnA Maker スタックにリンクすることはできます。

1. Azure portal で新しい Azure Search リソースを作成し、必要な SKU を選択します。

    ![QnA Maker Azure Search リソース](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. 元の Azure Search リソースのインデックスを新しいリソースに復元します。 [こちら](https://github.com/pchoudhari/QnAMakerBackupRestore)でバックアップ復元のサンプル コードを参照してください。

3. データが復元されたら、新しい Azure Search リソースに移動し、 **[キー]** を選択して、 **[名前]** と **[管理者キー]** を書き留めます。

    ![QnA Maker Azure Search キー](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. 新しい Azure Search リソースを QnA Maker スタックにリンクするために、QnA Maker App サービスに移動します。

    ![QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. **[アプリケーションの設定]** を選択し、 **[AzureSearchName]** フィールドと **[AzureSearchAdminKey]** フィールドを手順 3. で書き留めた値に置き換えます。

    ![QnA Maker App Service の設定](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. App Service を再起動します。

    ![QnA Maker App Service の再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker API の使用](../Quickstarts/csharp.md)
