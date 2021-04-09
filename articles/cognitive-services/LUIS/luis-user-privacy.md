---
title: データのエクスポートと削除 - LUIS
titleSuffix: Azure Cognitive Services
description: お客様は、データの表示、エクスポート、削除を完全に制御できます。 プライバシーとコンプライアンスを確保するために顧客データを削除します。
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/10/2020
ms.openlocfilehash: 0a2d0ce683261ca3460c7aeaa0d7a42152b81a1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98680188"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Cognitive Services で Language Understanding (LUIS) 内の顧客データをエクスポートおよび削除する

プライバシーとコンプライアンスを確保するために顧客データを削除します。

## <a name="summary-of-customer-data-request-features"></a>顧客データ要求機能の概要
Language Understanding Intelligent Service (LUIS) はサービスを運用するために顧客コンテンツを保持していますが、LUIS ユーザーは自分のデータの表示、エクスポート、削除を完全に制御できます。 この操作は、LUIS Web [ポータル](luis-reference-regions.md)または [LUIS Authoring (Programmatic とも呼ばれる) API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) を使って行うことができます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

顧客コンテンツは、リージョンの Microsoft Azure Storage に暗号化されて格納され、次のものが含まれます。

- 登録時に収集されたユーザー アカウントのコンテンツ
- モデルの構築に必要なトレーニング データ
- モデルの改善に役立つ[アクティブ ラーニング](luis-concept-review-endpoint-utterances.md)で使用される、記録されるユーザー クエリ
  - ユーザーは、要求に `&log=false` を追加することによってクエリのログ記録を無効にできます。詳しくは、[こちら](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)をご覧ください。

## <a name="deleting-customer-data"></a>顧客データの削除
LUIS ユーザーは、LUIS Web ポータルまたは LUIS Authoring (Programmatic とも呼ばれる) API を使用して、ユーザー コンテンツの削除を完全に制御できます。 次の表では、両方を支援するリンクを示します。

| | **ユーザー アカウント** | **Application** | **発話の例** | **エンドユーザー クエリ** |
| --- | --- | --- | --- | --- |
| **ポータル** | [リンク](luis-concept-data-storage.md#delete-an-account) | [リンク](luis-how-to-start-new-app.md#delete-app) | [リンク](luis-concept-data-storage.md#utterances-in-an-intent) | [アクティブ ラーニング発話](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[記録された発話](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>顧客データのエクスポート
LUIS ユーザーは、ポータルでデータの表示を完全に制御できますが、LUIS Authoring (Programmatic とも呼ばれる) API を使ってエクスポートする必要があります。 次の表では、LUIS Authoring (Programmatic とも呼ばれる) API でのデータのエクスポートに役立つリンクを示します。

| | **ユーザー アカウント** | **Application** | **発話** | **エンドユーザー クエリ** |
| --- | --- | --- | --- | --- |
| **API** | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [リンク](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>アクティブ ラーニングの場所

[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning)を有効にするために、公開済みの LUIS エンドポイントで受信されたユーザーの記録された発話は、次の Azure の地域に格納されます。

* [ヨーロッパ](#europe)
* [オーストラリア](#australia)
* [米国](#united-states)

アクティブ ラーニング データ (以下に詳述) を除いて、LUIS は[地域サービス向けのデータ ストレージ プラクティス](https://azuredatacentermap.azurewebsites.net/)に従います。

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>ヨーロッパ

Europe Authoring (Programmatic API とも呼ばれる) のリソースは Azure のヨーロッパ地域でホストされており、次の Azure 地域へのエンドポイントのデプロイをサポートしています。

* ヨーロッパ
* フランス
* イギリス

これらの Azure 地域にデプロイすると、アプリのエンド ユーザーからエンドポイントが受信した発話は、アクティブ ラーニングのために Azure のヨーロッパ地域に格納されます。

### <a name="australia"></a>オーストラリア

Australia Authoring (Programmatic API とも呼ばれる) のリソースは Azure のオーストラリア地域でホストされており、次の Azure 地域へのエンドポイントのデプロイをサポートしています。

* オーストラリア

これらの Azure 地域にデプロイすると、アプリのエンド ユーザーからエンドポイントが受信した発話は、アクティブ ラーニングのために Azure のオーストラリア地域に格納されます。

### <a name="united-states"></a>アメリカ合衆国

米国 Authoring (Programmatic API とも呼ばれる) のリソースは Azure の米国地域でホストされており、次の Azure 地域へのエンドポイントのデプロイをサポートしています。

* ヨーロッパまたはオーストラリアのオーサリング リージョンではサポートされていない Azure 地域

これらの Azure 地域にデプロイすると、アプリのエンド ユーザーからエンドポイントが受信した発話は、アクティブ ラーニングのために Azure の米国地域に格納されます。 

## <a name="disable-active-learning"></a>アクティブ ラーニングを無効にする

アクティブ ラーニングを無効にするには、「[アクティブ ラーニングを無効にする](luis-how-to-review-endpoint-utterances.md#disable-active-learning)」を参照してください。 格納されている発言を管理するには、「[Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance)」 (発話を削除する) を参照してください。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [LUIS リージョン リファレンス](./luis-reference-regions.md)
