---
title: コード サンプル - Content Moderator (.NET)
description: .NET アプリケーションから SDK を通じて Content Moderator を使用します。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: f2b5fb5c44cbb2e0d766c49ed6b715484c22f62e
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756737"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK サンプル

次のリストは、Azure Content Moderator for .NET を使用してビルドされたコード サンプルへのリンクです。

- **ヘルパー ライブラリ**: [他のサンプルで使用する Content Moderator クライアントを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs)。 [クイック スタート](content-moderator-helper-quickstart-dotnet.md)のページを参照してください。

## <a name="moderation"></a>モデレート

- **画像のモデレート**: [成人向けのきわどいコンテンツ、テキスト、および顔の画像を評価します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs)。 [クイック スタート](image-moderation-quickstart-dotnet.md)のページを参照してください。
- **カスタム画像**: [カスタム画像のリストを使用してモデレートします](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs)。 [クイック スタート](image-lists-quickstart-dotnet.md)のページを参照してください。

> [!NOTE]
> **画像リスト数は 5 個**、各リストの**画像数は 10,000 個**という上限があります。
>

- **テキストのモデレート**: [不適切な表現と個人データを検査します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs)。 [クイック スタート](text-moderation-quickstart-dotnet.md)のページを参照してください。
- **カスタム用語**: [カスタム用語のリストを使用してモデレートします](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs)。 [クイック スタート](term-lists-quickstart-dotnet.md)のページを参照してください。

> [!NOTE]
> 上限は**用語の一覧が 5 つ**で、各一覧では**用語が 10,000 個を超えてはいけません**。
>

- **ビデオのモデレート**: [成人向けのきわどいコンテンツのビデオをスキャンし、結果を取得します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs)。 [クイック スタート](video-moderation-api.md)のページを参照してください。

## <a name="review"></a>レビュー

- **画像ジョブ**: [スキャンしてレビューを作成するモデレート ジョブを開始します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs)。 [クイック スタート](moderation-jobs-quickstart-dotnet.md)のページを参照してください。
- **画像のレビュー**: [人がモデレート作業を行うためのレビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs)。 [クイック スタート](moderation-reviews-quickstart-dotnet.md)のページを参照してください。
- **ビデオ レビュー**: [人がモデレート作業を行うためのビデオ レビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs)。 [クイック スタート](video-reviews-quickstart-dotnet.md)のページを参照してください
- **ビデオ トランスクリプト レビュー**: [人がモデレート作業を行うためのビデオ トランスクリプト レビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs)。[クイック スタート](video-reviews-quickstart-dotnet.md)のページを参照してください

すべての .NET サンプルは、[GitHub にある Content Moderator の .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)に関するページでご覧ください。
