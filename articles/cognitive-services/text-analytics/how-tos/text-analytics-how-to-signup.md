---
title: Text Analytics API へのサインアップ
titleSuffix: Azure Cognitive Services
description: サインアップして Text Analytics サービスを使用するための手順。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 53532a19482a33f8727e71d44ae169225b5b1c98
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449965"
---
# <a name="how-to-sign-up-for-the-text-analytics-api"></a>Text Analytics API へのサインアップ方法

Text Analytics のリソースは、曜日を問わず 24 時間クラウドで利用できます。 解析対象のコンテンツをアップロードするためには、あらかじめサインアップしてアクセス キーを入手する必要があります。 API を呼び出す際は、その都度、アクセス キーが要求に必要となります。

+ まず Azure サブスクリプションを用意します。 [無料アカウント](https://azure.microsoft.com/free/)を作成して、無料で試すことができます。

+ [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。その際、**[Text Analytics API]** を選択します。 サインアップするとキーが生成されます。

Text Analytics には、調査と評価を目的とした Free レベルと、運用環境のワークロードを目的とした請求対象のレベルとがあります。 それぞれのサブスクリプションで複数のサインアップ (1 つは無料、もう 1 つは有料など) を行うことができます。 要求の量が増加した場合は、より多くのトランザクション数が提供されているレベルに切り替えることができます。

プレビューや Free レベルのサービスには、サービス レベル アグリーメントはありません。 詳細については、「[Cognitive Services の SLA](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)」を参照してください。

## <a name="how-to-change-tiers"></a>レベルの変更方法

最初は Free レベルを使用し、その後、運用環境のワークロードを目的とした課金対象のレベルに移行します。 標準的な請求は、段階的なレベルで行われます。 レベルを切り替えても、エンドポイントとアクセス キーは変わりません。

1. [Azure portal](https://portal.azure.com) にサインインして[目的のサービスを検索](text-analytics-how-to-access-key.md)します。

2. **[価格帯]** をクリックします。

   ![左側のナビゲーション メニューの価格帯コマンド](../media/portal-pricing-tier.png)

3. 価格帯を選択し、**[選択]** をクリックします。  選択内容が処理されるとすぐに新しい制限が有効となります。 

   ![価格帯選択ページのタイルと [選択] ボタン](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>請求体系について

請求は、トランザクション数に基づいて行われます。 まず、特定のレベルで一定数のトランザクションを月ごとの請求サイクルで購入し、それを超過した場合には、トランザクション単位で細かく超過料金が適用されます。 高頻度で上限を超えるようであれば、上位のレベルへの切り替えを検討してください。

詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)を参照してください。

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>Text Analytics API で 1 トランザクションとなるものは何ですか?
1 ドキュメントに対する注釈はすべて、1 トランザクションとしてカウントされます。 バッチ スコアリング呼び出しでは、そのトランザクションでスコア付けする必要があるドキュメントの数も考慮されます。 そのため、たとえば 1 つの API 呼び出しで感情分析のために 1,000 ドキュメントが送信される場合、1,000 トランザクションとしてカウントされます。

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アクセス キーを取得する](text-analytics-how-to-access-key.md)
