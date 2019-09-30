---
title: Azure Cognitive Services とは
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services は、インテリジェントなアプリケーションを構築するために、Microsoft Azure とともに使用できる API、SDK、およびサービスです。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 04/19/2019
ms.author: nitinme
ms.openlocfilehash: 5337f2d3df395650c65ae4a12cbdf4f8ba90ca3d
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840399"
---
# <a name="what-are-azure-cognitive-services"></a>Azure Cognitive Services とは

Azure Cognitive Services は、開発者が直接的な AI またはデータ サイエンスのスキルや知識がなくてもインテリジェントなアプリケーションを構築するために使用できる API、SDK、およびサービスです。 Azure Cognitive Services によって、開発者は簡単にアプリケーションにコグニティブ機能を追加できます。 Azure Cognitive Services の目標は、開発者が、聞いたり、話したり、理解したり、推論し始めたりできるアプリケーションの作成を支援することです。 Azure Cognitive Services 内のサービス カタログは、5 つの主要な柱として、視覚、音声、言語、Web 検索、および意思決定に分類できます。

## <a name="vision-apis"></a>Vision API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Computer Vision")|Computer Vision サービスを使用すると、イメージを処理して情報を返すための高度なアルゴリズムにアクセスできます。|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Custom Vision Service")|Custom Vision Service を使用すると、カスタム画像分類器を構築できます。|
|[Face API](https://docs.microsoft.com/azure/cognitive-services/face/ "Face API")|Face API は、顔属性の検出と認識を有効にする、高度な顔アルゴリズムへのアクセスを提供します。|
|[Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Form Recognizer") (プレビュー)|Form Recognizer では、キーと値のペアおよびテーブル データを識別して form ドキュメントから抽出し、その後、元のファイルにあるリレーションシップを含む構造化データを出力します。|
|[Ink Recognizer](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Ink Recognizer") (プレビュー)|Ink Recognizer では、デジタル インク ストローク データ、シェイプ、および手書きのコンテンツを認識して分析し、認識されたすべてのエンティティを備えたドキュメント構造を出力します。|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|Video Indexer は、ビデオから洞察を抽出できます。|

## <a name="speech-apis"></a>Speech API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Speech Services")|音声サービスは、音声対応の機能をアプリケーションに追加します。|
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Speaker Recognition API") (プレビュー)|Speaker Recognition API は、話者識別と検証のアルゴリズムを提供します。|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing Speech") (廃止)|Bing Speech API は、アプリケーションに音声対応の機能を作成する簡単な方法を提供します。|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech") (廃止)|Translator Speech は、機械翻訳サービスです。|

## <a name="language-apis"></a>言語 API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Language Understanding (LUIS) サービスを使用すると、アプリケーションが人の発言の意図を認識できるようになります。|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker を使用すると、半構造化コンテンツから質疑応答サービスを構築できます。|
|[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Text Analytics")|Text Analytics は、未加工のテキストに、センチメント分析、キー フレーズ抽出、および言語検出のための自然言語処理を提供します。|
|[Translator Text](https://docs.microsoft.com/azure/cognitive-services/translator/ "Translator Text")|Translator Text は、ほぼリアルタイムでのマシン ベースのテキスト翻訳を提供します。|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|Video Indexer は、ビデオから洞察を抽出できます。|

## <a name="search-apis"></a>Search API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Bing News Search](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Bing News Search")|Bing News Search は、ユーザーのクエリに関連すると判断されたニュース記事の一覧を返します。|
|[Bing Video Search](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Bing Video Search")|Bing Video Search は、ユーザーのクエリに関連すると判断されたビデオの一覧を返します。|
|[Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Bing Web Search")|Bing Web Search は、ユーザーのクエリに関連すると判断された検索結果の一覧を返します。|
|[Bing Autosuggest](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Bing Autosuggest")|Bing Autosuggest は、部分的な検索クエリの用語を Bing に送信し、クエリ候補の一覧を取得できます。|
|[Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Bing Custom Search")|Bing Custom Search は、関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成できます。|
|[Bing Entity Search](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Bing Entity Search")|Bing Web Search では、ユーザーのクエリに関連があると Bing が判断したエンティティに関する情報が返されます。|
|[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Bing Image Search")|Bing Image Search は、ユーザーのクエリに関連すると判断されたイメージの表示を返します。|
|[Bing Visual Search](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Bing Visual Search")|Bing Visual Search は、画像 (見た目が似ている画像、画像内にある製品のショッピング ソース、関連検索) に関する分析情報を返します。|
|[Bing Local Business Search](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| Bing Local Business Search API を使用すると、アプリケーションでは検索クエリに基づいて地元企業に関する連絡先や場所の情報を検索できます。|
|[Bing Spell Check](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Bing Spell Check")|Bing Spell Check を使用して、コンテキストに応じた文法およびスペル チェックを実行できます。|

## <a name="decision-apis"></a>Decision API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Anomaly Detector](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Anomaly Detector") (プレビュー)|Anomaly Detector では、時系列データを監視し、その中の異常を検出できます。|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator は、不快感を与えたり、望ましくなかったり、危険であったりする可能性があるコンテンツを監視します。|
|[Personalizer](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizer") (プレビュー)|Personalizer は、ユーザーのリアルタイムの動作から学習し、ユーザーに表示する最良のエクスペリエンスを選択できるようにします。|

## <a name="use-free-trials"></a>無料試用版の使用

[無料試用版のサインアップ](https://azure.microsoft.com/try/cognitive-services/ "サインアップのヘルプ")に必要なのは、電子メールといくつかの簡単な手順だけです。 Microsoft アカウントをまだお持ちでない場合は、それが必要です。 要求された各 API の一意のキー ペアを受け取ります。 2 つ目は単なるスペアです。 秘密鍵は誰とも共有しないでください。 試用版には、秒または分あたりのトランザクション数の速度制限と、月間使用量の上限の両方があります。 トランザクションは単なる API 呼び出しです。 有料プランにアップグレードすれば制限を解除できます。

## <a name="subscription-management"></a>サブスクリプション管理

Microsoft Account にサインインすると、[個人用サブスクリプション](https://www.microsoft.com/cognitive-services/subscriptions "個人用サブスクリプション")にアクセスして、使用している製品、残りのクォータ、およびサブスクリプションにさらに製品を追加する機能を表示できます。

## <a name="upgrade-to-unlock-limits"></a>アップグレードして制限を解除する

すべての API には、使用量とスループットの制限が設けられた無料試用プランが用意されています。  有料プランを使用し、サービスをデプロイするときに Azyre portal 上で適切な価格レベルのオプションを選択することで、これらの制限を引き上げることができます。 [プランと価格](https://azure.microsoft.com/pricing/details/cognitive-services/ "プランと価格")に関するページをご覧ください。 クレジット カードと電話番号で、Azure サブスクリプション アカウントを設定する必要があります。 特殊な要件がある場合や、単に販売員と話したい場合は、料金ページの上部にある「問い合わせ」ボタンをクリックしてください。

## <a name="regional-availability"></a>リージョン別の提供状況

Cognitive Services の API は、Microsoft マネージド データ センターの拡大しているネットワーク上でホストされます。 [Azure リージョン一覧](https://azure.microsoft.com/regions)で API ごとのリージョン別の提供状況がわかります。

まだサポートされていないリージョンを探しているのですか。 [UserVoice フォーラム](https://cognitive.uservoice.com/)に機能要求を記入してご連絡ください。

## <a name="supported-cultural-languages"></a>サポートされる言語 (カルチャ)

 Cognitive Services では、さまざまなカルチャ言語がサービス レベルでサポートされています。 各 API で利用可能な言語は、[サポート対象の言語一覧](language-support.md)に関するページで検索できます。

## <a name="container-support"></a>コンテナー サポート

 Cognitive Services には、Azure クラウドまたはオンプレミスへのデプロイ用にコンテナーが用意されています。 詳細については、[Cognitive Services のコンテナー](cognitive-services-container-support.md)に関するページを参照してください。

## <a name="certifications-and-compliance"></a>認定資格とコンプライアンス

Cognitive Services には、CSA STAR Certification、FedRAMP Moderate、HIPAA BAA などの認定資格が与えられています。

自分で監査したり、セキュリティを確認する目的で認定資格を[ダウンロード](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)できます。

プライバシーやデータ管理は[セキュリティ センター](https://servicetrust.microsoft.com/)で確認できます。

## <a name="support"></a>サポート

* サポートおよび技術的な質問については、 [Stack Overflow](https://stackoverflow.com/questions/tagged/microsoft-cognitive) に投稿してください
* フィードバックや機能要求については、[UserVoice](https://cognitive.uservoice.com/) をご覧ください

## <a name="next-steps"></a>次の手順

* [Cognitive Services アカウントを作成する](cognitive-services-apis-create-account.md)
