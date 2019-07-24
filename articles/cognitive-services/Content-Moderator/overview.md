---
title: Azure Content Moderator とは
titlesuffix: Azure Cognitive Services
description: ユーザーによって生成されたコンテンツに含まれる不適切な素材の追跡、フラグ設定、評価、フィルタリングを Content Moderator を使用して行う方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 440471acb6e122bf25ba21b0ab3b5a2f7d9b021d
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758141"
---
# <a name="what-is-azure-content-moderator"></a>Azure Content Moderator とは

Azure Content Moderator API は、テキスト、画像、ビデオのコンテンツに不適切な内容、リスクのある内容、その他望ましくない可能性のある内容が含まれているかどうかを確認できるコグニティブ サービスです。 このサービスでは、そのような内容が検出されると、それに応じたラベル (フラグ) がコンテンツに適用されます。 その後は、法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、フラグの設定されたコンテンツをアプリで処理することができます。 コンテンツに適用されるフラグがそれぞれどのような意味であるかについての詳細は、「[Content Moderator の API](#content-moderator-apis)」のセクションを参照してください。

## <a name="where-it-is-used"></a>使用場所

ソフトウェア開発者またはチームが Content Moderator を使用するシナリオをいくつか次に示します。

- ユーザーが生成した製品カタログなどのコンテンツをモデレーションするオンライン マーケットプレース
- ユーザーが生成したゲームの成果物とチャット ルームをモデレーションするゲーム会社
- ユーザーによって追加された画像、テキスト、ビデオをモデレーションするソーシャル メッセージング プラットフォーム
- コンテンツに対する一元的なモデレーションを実装するエンタープライズ メディア企業
- 学生および教育者にとって不適切なコンテンツをフィルター処理して除外する K-12 教育ソリューション プロバイダー

## <a name="what-it-includes"></a>備えている機能

Content Moderator サービスは、REST 呼び出しと .NET SDK のどちらを介しても利用できるいくつかの Web サービス API で構成されています。 ほかには目視レビュー ツールが用意されており、レビュー担当者がサービスを補助したり、モデレーション機能を調整したりできるようになっています。

![モデレーション用 API、レビュー用 API、目視レビュー ツールを示した Content Moderator のブロック図](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>Content Moderator の API

Content Moderator サービスには次のシナリオに対応するための API が含まれています。

| API グループ | 説明 |
| ------ | ----------- |
|[**テキストのモデレーション**](text-moderation-api.md)| テキストをスキャンし、不適切な表現、露骨に性的な表現、性的な内容を暗示する表現、下品な表現、個人データが含まれているかどうかを確認します。|
|[**カスタム用語リスト**](try-terms-list-api.md)| テキストをスキャンし、組み込みの用語に加え、カスタムの用語リストと照合します。 カスタム リストは、独自のコンテンツ ポリシーに従ってコンテンツをブロックまたは許可する際に使用します。|  
|[**画像のモデレーション**](image-moderation-api.md)| 画像をスキャンして成人向けのコンテンツやわいせつなコンテンツが含まれているかどうかを確認するほか、光学式文字認識 (OCR) 機能により画像内のテキストを検出したり、顔検出を実行したりします。|
|[**カスタム画像リスト**](try-image-list-api.md)| 画像をスキャンし、画像のカスタム リストと照合します。 カスタム画像リストを使うと、何度も出現し、そのたびに分類をするのが煩わしいコンテンツを除去できます。|
|[**ビデオのモデレーション**](video-moderation-api.md)| ビデオをスキャンして成人向けのコンテンツやわいせつなコンテンツが含まれているかどうかを確認し、そのようなコンテンツがあればタイム マーカーを返します。|
|[**API の確認**](try-review-api-job.md)| [ジョブ](try-review-api-job.md)、[レビュー](try-review-api-review.md)、[ワークフロー](try-review-api-workflow.md)の操作を使用して、目視レビュー ツールを使った人間参加型のワークフローを作成したり、自動化したりできます。 Workflow API は、.NET SDK ではまだ利用できません。|

### <a name="review-tool"></a>レビュー ツール

Content Moderator サービスには、ほかにも Web ベースの[レビュー ツール](Review-Tool-User-Guide/human-in-the-loop.md)が備わっています。このツールは、ヒューマン モデレーターによって処理されるコンテンツ レビューのホストとなります。 人間による入力がサービスに対するトレーニングになることはありませんが、このサービスの成果と目視レビュー チームの作業を組み合わせれば、開発者が効率性と正確性をうまく両立させることが可能になります。 また、レビュー ツールは、さまざまな Content Moderator リソース向けに、ユーザー フレンドリなフロントエンドを備えています。

![Content Moderator 目視レビュー ツールのホームページ](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Content Moderator サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次の手順

「[Content Moderator の概要](quick-start.md)」の手順に従って Content Moderator サービスを使ってみましょう。