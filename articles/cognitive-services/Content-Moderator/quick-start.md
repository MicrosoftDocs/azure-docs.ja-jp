---
title: クイック スタート:Web 上で Content Moderator を試す
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、オンライン Content Moderator レビュー ツールを使用して、コードを記述せずに Content Moderator の基本的な機能をテストします。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Content Moderator, コンテンツ モデレーション
ms.openlocfilehash: f8ad9c135fea4e582e6ba47764d0401936f8c295
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221178"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>クイック スタート:Web 上で Content Moderator を試す

このクイックスタートでは、オンライン Content Moderator レビュー ツールを使用して、コードを記述せずに Content Moderator の基本的な機能をテストします。 このサービスをより迅速にご利用のコンテンツ モデレーション アプリに統合する場合は、[次のステップ](#next-steps)のセクションのその他のクイック スタートを参照してください。

## <a name="prerequisites"></a>前提条件

- Web ブラウザー

## <a name="set-up-the-review-tool"></a>レビュー ツールのセットアップ
Content Moderator レビュー ツールは、レビューア担当者が意思決定における Cognitive Service を支援できる Web ベースのツールです。 このガイドでは、レビュー ツールを設定する簡単なプロセスを体験して、Content Moderator サービスの動作方法を確認します。 [Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)のサイトに移動し、サインアップします。

![Content Moderator ホーム ページ](images/homepage.PNG)

## <a name="create-a-review-team"></a>レビュー チームを作成する

次に、レビュー チームを作成します。 作業のシナリオでは、このチームは、サービスのモデレーションに関する意思決定を手動でレビューするユーザーのグループになります。 チームを作成するには、 **[リージョン]** を選択し、 **[チーム名]** と **[チーム ID]** を指定します。 仕事仲間をチームに招待する場合は、仲間のメール アドレスをここに入力します。

> [!NOTE]
> **[チーム名]** は、ご自分のレビュー チームのフレンドリ名です。 この名前は Azure portal に表示されます。 **[チーム ID]** は、ご自分のレビュー チームのプログラムでの識別に使用されます。

> [!div class="mx-imgBorder"]
> ![チーム メンバーを招待する](images/create-team.png)

カスタマー マネージド キー (CMK) を使用してデータを暗号化することを選択した場合は、E0 価格レベルのご自分の Content Moderator リソースの **[リソース ID]** の入力が求められます。 指定するリソースは、このチームに対して一意である必要があります。 

> [!div class="mx-imgBorder"]
> ![カスタマー マネージド キーを使用してチーム メンバーを招待する](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>サンプル コンテンツのアップロード

サンプル コンテンツをアップロードする準備ができました。 **[Try > Image] (トライ > イメージ)** 、 **[Try > Text] (トライ > テキスト)** 、または **[Try > Video] (トライ > ビデオ)** .を選択します。

> [!div class="mx-imgBorder"]
> ![画像またはテキスト モデレーションを試す](images/tryimagesortext.png)

モデレート用にコンテンツを送信します。 次のサンプル テキスト コンテンツを使用できます。

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

レビュー ツールは内部で、モデレート API を呼び出し、コンテンツをスキャンします。 スキャンが完了したら、レビューを待機している結果があることを通知するメッセージが表示されます。

> [!div class="mx-imgBorder"]
> ![ファイルをモデレートする](images/submitted.png)

## <a name="review-moderation-tags"></a>モデレート タグのレビュー

適用したモデレート タグをレビューします。 コンテンツに適用されたタグおよび各カテゴリのスコアを確認できます。 さまざまなコンテンツ タグによって示される内容の詳細については、[イメージ](image-moderation-api.md)、[テキスト](text-moderation-api.md)、および[ビデオ](video-moderation-api.md)のモデレーション トピックを参照してください。

<!-- ![Review results](images/reviewresults_text.png) -->

プロジェクトでは、ユーザーまたはユーザーのレビュー チームは、必要に応じてタグを変更したり、タグを追加したりできます。 **次へ** ボタンでこれらの変更を送信します。 ビジネス アプリケーションが Moderator API を呼び出すと、タグ付きコンテンツは、ここでキューに入り、人間のレビュー チームにレビューされる用意が整えられます。 このアプローチを使用して、大量のコンテンツをすばやくレビューできます。

ここまでで、Content Moderator レビュー ツールを使用して Content Moderator サービスで実行できる内容の例を確認しました。 次に、レビュー ツールについてさらに学習し、レビュー API を使用してソフトウェア プロジェクトに統合する方法を学習するか、または[次の手順](#next-steps)のセクションまでスキップし、アプリで Moderation API 自体を使用する方法を学習できます。

## <a name="learn-more-about-the-review-tool"></a>レビュー ツールについてさらに学習する

Content Moderator レビュー ツールを使用する方法についてさらに学習するには、[レビュー ツール](Review-Tool-User-Guide/human-in-the-loop.md) ガイドを参照し、レビュー ツール API を参照して人間のレビュー エクスペリエンスを微調整する方法について学習します。
- [ジョブ API](try-review-api-job.md) は、モデレーション API を使用することによりコンテンツをスキャンし、レビュー ツールでレビューを生成します。 
- [レビュー API](try-review-api-review.md) は、最初にコンテンツをスキャンすることなく、人間の モデレーター用の画像、テキスト、またはビデオ レビューを直接作成します。 
- [ワークフロー API](try-review-api-workflow.md) は、自分のチームが作成するカスタム ワークフローに関する詳細を作成、更新、取得します。

または、次の手順に進み、コードでの Moderation API の使用を開始します。

## <a name="next-steps"></a>次のステップ

アプリで Moderation API 自体を使用する方法を学習します。
- 画像のモデレートを実装します。 [API コンソール](try-image-api.md)を使用するか、[クイックスタート](client-libraries.md)に従って、画像をスキャンして、タグ、信頼度スコア、および他の抽出された情報を使用することによって、成人向けや猥褻な可能性のあるコンテンツを検出します。
- テキストのモデレートを実装します。 [API コンソール](try-text-api.md)を使用するか、[クイックスタート](client-libraries.md)に従って、テキスト コンテンツをスキャンして、不適切な言葉、個人データ、およびその他の望ましくないテキストを検索します。
- ビデオのモデレートを実装します。 [C# 用のビデオのモデレートに関するハウツー ガイド](video-moderation-api.md)に関するページ従ってビデオをスキャンし、成人向けやきわどいコンテンツの可能性があるものを検出します。 
