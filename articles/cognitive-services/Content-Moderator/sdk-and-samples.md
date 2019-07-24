---
title: SDK とサンプル - Content Moderator、Python、Java、Node.js、.NET
titlesuffix: Azure Cognitive Services
description: Content Moderator 用の SDK とサンプルを取得する
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: fd71a48372bcdb459bb3b7509e9a9c5dba529555
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756603"
---
# <a name="content-moderator-sdks-and-samples"></a>Content Moderator SDK とサンプル

## <a name="sdks-for-python-java-nodejs-and-net"></a>Python、Java、Node.js、および .NET 用の SDK

- [Content Moderator SDK for Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Content Moderator SDK for Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Content Moderator SDK for Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>.NET SDK のサンプル

次のリストは、Azure Content Moderator for .NET を使用してビルドされたコード サンプルへのリンクです。

- **ヘルパー ライブラリ**: [他のサンプルで使用する Content Moderator クライアントを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs)。 [クイック スタート](content-moderator-helper-quickstart-dotnet.md)のページを参照してください。

### <a name="moderation"></a>モデレート 
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

### <a name="review"></a>レビュー
- **画像ジョブ**: [スキャンしてレビューを作成するモデレート ジョブを開始します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs)。 [クイック スタート](moderation-jobs-quickstart-dotnet.md)のページを参照してください。
- **画像のレビュー**: [人がモデレート作業を行うためのレビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs)。 [クイック スタート](moderation-reviews-quickstart-dotnet.md)のページを参照してください。
- **ビデオ レビュー**: [人がモデレート作業を行うためのビデオ レビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs)。 [クイック スタート](video-reviews-quickstart-dotnet.md)のページを参照してください
- **ビデオ トランスクリプト レビュー**: [人がモデレート作業を行うためのビデオ トランスクリプト レビューを作成します](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs)。[クイック スタート](video-reviews-quickstart-dotnet.md)のページを参照してください

すべての .NET サンプルは、[GitHub にある Content Moderator の .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)に関するページでご覧ください。

## <a name="rest-api-samples-in-c"></a>C# での REST API のサンプル

- [Image moderation](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Text moderation](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Video moderation](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [画像のレビュー](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [画像ジョブ](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

これらのサンプルのチュートリアルについては、[オンデマンド ウェビナー](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)に関する説明を参照してください。

## <a name="tutorials"></a>チュートリアル
- [e コマース カタログのモデレーション](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)。 [チュートリアル](ecommerce-retail-catalog-moderation.md)を参照してください。
- [Facebook のコンテンツ モデレーション](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)。 [チュートリアル](facebook-post-moderation.md)を参照してください。
- [ビデオとトランスクリプトのモデレーションとレビュー ソリューション](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp)。[チュートリアル](video-transcript-moderation-review-tutorial-dotnet.md)を参照してください。

## <a name="on-demand-webinars"></a>オンデマンド ウェビナー
- [Content Moderator を使用した大規模なマシン支援型コンテンツ モデレーション](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
