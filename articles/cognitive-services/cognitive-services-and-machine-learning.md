---
title: Cognitive Services と機械学習
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services が機械学習用の他の Azure サービスに適合する部分について説明します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 1fc21b6338accef8e22b2e41ad2017d24ab8bd38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710466"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services と機械学習

Cognitive Services は、一般的な問題を解決する機械学習機能を備えています。たとえば、テキストで感情 (センチメント) を分析したり、画像を分析して物や顔を認識したりすることができます。 これらのサービスを使用するために、機械学習やデータ サイエンスに関する特別な知識は必要ありません。 

[Cognitive Services](./what-are-cognitive-services.md) はサービスの集合であり、それぞれが異なる一般化予測機能をサポートします。 適切なサービスを見つけやすくするために、サービスは複数のカテゴリに分類されています。 

|サービス カテゴリ|目的|
|--|--|
|[Decision](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|情報に基づく、効率的な意思決定のためのレコメンデーションを提示するアプリを構築します。|
|[Language](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|お使いのアプリが、構築済みスクリプトで自然言語を処理し、センチメントを評価し、ユーザーの求めるものを認識する方法を学習できるようにします。|
|[Search](https://azure.microsoft.com/services/cognitive-services/directory/search/)|お使いのアプリに Bing Search API を組み込み、1 つの API 呼び出しで何十億もの Web ページ、画像、動画、ニュースをくまなく調べる機能を実装します。|
|[Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|音声をテキストに変換し、テキストを自然な音声に変換します。 ある言語を別の言語に翻訳し、話者の認証と認識を可能にします。|
|[Vision](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|写真、動画、デジタル インク コンテンツの認識、識別、キャプションの追加、インデックスの作成、モデレートを行います。|
||||

Cognitive Services は次の場合に使用します。

* 一般化されたソリューションを使用できる。
* プログラミング REST API または SDK からソリューションにアクセスする。 

次の場合は別の機械学習ソリューションを使用してください。

* アルゴリズムを選んで、特殊なデータをトレーニングする必要がある。

## <a name="what-is-machine-learning"></a>機械学習とは

機械学習は、データとアルゴリズムを組み合わせて特定のニーズを解決するという概念です。 データとアルゴリズムがトレーニングされると、別のデータで再利用できるモデルが出力されます。 トレーニングされたモデルから、新しいデータに基づいた分析情報を得ることができます。 

機械学習システムを構築するプロセスでは、機械学習やデータ サイエンスの知識がある程度必要になります。

機械学習は、[Azure Machine Learning (AML) の製品とサービス](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context)を通じて提供されます。

## <a name="what-is-a-cognitive-service"></a>Cognitive Services とは

Cognitive Services は、機械学習ソリューションのコンポーネントであるデータ、アルゴリズム、トレーニング済みモデルの一部または全部を提供します。 これらのサービスは、データに関する一般的な知識が前提になりますが、機械学習やデータ サイエンスの経験は不要です。 これらのサービスでは REST API と言語ベースの SDK の両方を提供しています。 そのため、これらのサービスを使用するにはプログラミング言語の知識が必要です。

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Cognitive Services と Azure Machine Learning (AML) の類似点

実現方法はそれぞれのオファリングで異なりますが、どちらも人工知能 (AI) を業務の強化に応用するという最終目標があります。 

一般に、対象ユーザーは異なります。

* Cognitive Services は機械学習の経験がない開発者を対象としています。
* Azure Machine Learning はデータ サイエンティスト向けに特化されています。 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Cognitive Services と機械学習の違い

Cognitive Services では、ユーザーに対してトレーニング済みのモデルが提供されます。 これはデータとアルゴリズムを統合したもので、REST API や SDK から利用できます。 シナリオによっては、このサービスを数分で実装できます。  Cognitive Services は、テキスト内のキー フレーズや画像内の項目識別といった一般的な問題を解決します。 

機械学習は、通常、適切に実装するために長時間を要するプロセスです。 Cognitive Services と同等の機能を実現するために、この時間を費やしてデータの収集、クリーニング、変換、アルゴリズムの選択、モデルのトレーニング、およびデプロイが行われます。 機械学習では、きわめて特殊な問題や具体的な問題を解決することが可能です。 機械学習の問題では、データ サイエンスの専門知識だけでなく、検討中の問題の特定の主題とデータについて理解する必要があります。

## <a name="what-kind-of-data-do-you-have"></a>保有するデータの種類

サービスの集合である Cognitive Services は、トレーニングされたモデルにカスタム データが不要な場合、一部必要である場合、または全部必要である場合があります。 

### <a name="no-additional-training-data-required"></a>追加のトレーニング データが不要

完全にトレーニングされたモデルを提供するサービスは、"_不透明のボックス_" として扱われます。 その仕組みやトレーニングに使用されたデータを知る必要はありません。 完全にトレーニングされたモデルに自分のデータを取り込むことで予測が得られます。 

### <a name="some-or-all-training-data-required"></a>トレーニング データが一部または全部必要

一部のサービスでは、自分のデータを取り込んでからモデルをトレーニングすることができます。 これにより、サービスのデータとアルゴリズムに自分のデータを加えてモデルを拡張できます。 出力はニーズに合ったものとなります。 自分のデータを取り込むときに、サービスに固有の方法でデータにタグを付ける必要がある場合があります。 たとえば、花を識別するようにモデルをトレーニングする場合は、花の画像のカタログを、各画像における花の位置と共に提供してモデルをトレーニングできます。 

あるサービスは、独自のデータを強化するためにユーザーにデータの提供を "_許可_" します。 あるサービスは、ユーザーにデータの提供を "_要求_" します。 

### <a name="real-time-or-near-real-time-data-required"></a>リアルタイムまたはほぼリアルタイムのデータが必要

サービスによっては、効果的なモデルを構築するために、リアルタイムまたはほぼリアルタイムのデータが必要になることがあります。 こうしたサービスでは大量のモデル データが処理されます。 

## <a name="service-requirements-for-the-data-model"></a>データ モデルに関するサービスの要件

次のデータは、各サービスが許可または要求するデータの種類でサービスを分類したものです。

|Cognitive Service|トレーニング データが不要|ユーザーがトレーニング データを一部または全部提供|リアルタイムまたはほぼリアルタイムでデータを収集|
|--|--|--|--|
|[Anomaly Detector](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Search |x|||
|[Computer Vision](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./custom-vision-service/overview.md)||x||
|[Face](./Face/Overview.md)|x|x||
|[Form Recognizer](./form-recognizer/overview.md)||x||
|[Immersive Reader](./immersive-reader/overview.md)|x|||
|[Ink Recognizer](/previous-versions/azure/cognitive-services/Ink-Recognizer/overview)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizer](./personalizer/what-is-personalizer.md)|○*|○*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Speaker Recognizer](./speech-service/speaker-recognition-overview.md)||x||
|[Speech のテキスト読み上げ (TTS)](speech-service/text-to-speech.md)|x|x||
|[Speech の音声テキスト変換 (STT)](speech-service/speech-to-text.md)|x|x||
|[音声翻訳](speech-service/speech-translation.md)|x|||
|[Text Analytics](./text-analytics/overview.md)|x|||
|[Translator](./translator/translator-info-overview.md)|x|||
|[Translator - Custom Translator](./translator/custom-translator/overview.md)||x||

*Personalizer は、(リアルタイムで動作するため) サービスが収集したトレーニング データだけでユーザーのポリシーとデータを評価します。 Personalizer の事前トレーニングやバッチ トレーニングには、大量の履歴データセットが必要ありません。 

## <a name="where-can-you-use-cognitive-services"></a>Cognitive Services を使用できる場所
 
このサービスは、REST API または SDK 呼び出しを行うことができるアプリケーションで使用されます。 たとえば、Web サイト、ボット、仮想現実や複合現実、デスクトップ アプリケーション、モバイル アプリケーションなどです。 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure Cognitive Search と Cognitive Search の関連性

[Azure Cognitive Search](../search/search-what-is-azure-search.md) は独立したクラウド検索サービスであり、必要に応じて Cognitive Services を使用して、イメージと自然言語の処理をインデックス作成ワークロードに追加します。 Cognitive Services は、個々の API をラップする[組み込みのスキル](../search/cognitive-search-predefined-skills.md)を通じて Azure Cognitive Search で公開されます。 チュートリアルには無料のリソースを使用できますが、ボリュームが大きい場合は[課金対象のリソース](../search/cognitive-search-attach-cognitive-services.md)を作成して接続するようにしてください。

## <a name="how-can-you-use-cognitive-services"></a>Cognitive Services の用途

各サービスからユーザーのデータに関する情報が提供されます。 サービスを組み合わせて複数のソリューションを連結できます。たとえば、音声 (オーディオ) をテキストに変換し、そのテキストを多数の言語に翻訳し、翻訳された言語でナレッジベースから回答を得ることができます。 Cognitive Services は、インテリジェントなソリューションを独自に作成するために使用できるほか、従来の機械学習プロジェクトと組み合わせてモデルを補完したり、開発プロセスを高速化したりすることもできます。 

他の機械学習ツールにモデルをエクスポートできる Cognitive Services:

|Cognitive Service|モデル情報|
|--|--|
|[Custom Vision](./custom-vision-service/overview.md)|Tensorflow for Android、CoreML for iOS11、ONNX for Windows ML に対して[エクスポート](./custom-vision-service/export-model-python.md)|

## <a name="learn-more"></a>詳細情報

* [アーキテクチャ ガイド - Microsoft の機械学習製品とは](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [機械学習 - ディープ ラーニングと機械学習の比較の概要](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>次のステップ

* [Azure portal](cognitive-services-apis-create-account.md) または [Azure CLI](./cognitive-services-apis-create-account-cli.md) で Cognitive Services のアカウントを作成する。
* コグニティブ サービスの[認証](authentication.md)方法を確認する。
* 問題の特定とデバッグに[診断ログ](diagnostic-logging.md)を使用する。 
* Docker [コンテナー](cognitive-services-container-support.md)に Cognitive Services をデプロイする。
* [サービスの更新情報](https://azure.microsoft.com/updates/?product=cognitive-services)で最新情報を入手する。