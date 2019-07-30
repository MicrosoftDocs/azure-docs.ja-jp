---
title: デプロイのリソース容量 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker サービスを作成する前に、前述したサービスに関して、どのレベルが現状に適しているかを判断する必要があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 3126613a9dca00d221610dc46116dd409c65d53d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446638"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>QnA Maker のデプロイに使用する容量の選択

QnA Maker サービスは、次の 3 つの Azure リソースに依存しています。
1.  App Service (ランタイム用)
2.  Azure Search (QnA の格納用)
3.  App Insights (省略可能。チャット ログとテレメトリの格納用)

QnA Maker サービスを作成する前に、前述したサービスに関して、どのレベルが現状に適しているかを判断する必要があります。 

通常、次の 3 つのパラメーターを考慮する必要があります。

1. **サービスに必要なスループット**: App Service の適切な[アプリ プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)を、実際のニーズに基づいて選択します。 アプリは[スケールアップ](https://docs.microsoft.com/azure/app-service/web-sites-scale)またはスケールダウンすることができます。 この点は、Azure Search の SKU の選択にも影響します。詳細については、[こちら](https://docs.microsoft.com/azure/search/search-sku-tier)を参照してください。

1. **ナレッジ ベースのサイズと数**: 実際のシナリオに合った適切な [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/) を選択してください。 特定のレベルで発行できるナレッジ ベースの数は N-1 件です (N は、そのレベルで許容される最大インデックス)。 レベルごとに許容されるドキュメントの最大サイズと最大数もチェックしてください。

    たとえば、レベルに 15 個の許可されたインデックスがある場合、14 個のナレッジ ベースを発行できます (発行されたナレッジ ベースあたり 1 インデックス)。 15 番目のインデックスは、作成およびテスト用にすべてのナレッジ ベースで使用されます。 

1. **ソースとしてのドキュメントの数**: QnA Maker 管理サービスの無料の SKU では、ポータルと API で管理できるドキュメントの数が 3 つ (サイズはそれぞれ 1 MB) に限定されます。 Standard SKU では、管理できるドキュメントの数に制限はありません。 詳細については、[こちら](https://aka.ms/qnamaker-pricing)を参照してください。

次の表は、いくつかの基本的なガイドラインを示したものです。

|                        | QnA Maker 管理 | App Service | Azure Search | 制限事項                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| 実験        | 無料の SKU             | Free レベル   | Free レベル    | 発行できる KB は 2 つまで (最大サイズ 50 MB)  |
| 開発/テスト環境   | Standard SKU         | 共有      | Basic        | 発行できる KB は 14 個まで (最大サイズ 2 GB)    |
| 運用環境 | Standard SKU         | Basic       | Standard     | 発行できる KB は 49 個まで (最大サイズ 25 GB) |

QnA Maker スタックのアップグレードについては、「[QnA Maker サービスのアップグレード](../How-To/upgrade-qnamaker-service.md)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker サービスのアップグレード](../How-To/upgrade-qnamaker-service.md)
