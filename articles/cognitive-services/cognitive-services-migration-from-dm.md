---
title: "DataMarket Recommendations API から Azure Cognitive Services Recommendations API への移行 | Microsoft Docs"
description: "Azure Machine Learning Recommendations-- Cognitive Service Recommendations への移行"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: ec9cc302-fef5-4b68-8f9b-fa73538d0424
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 8329cc800f685455be113521446e691b5798c1fc
ms.openlocfilehash: 072f92b186f32813eef9b17e742081b680ef86a3


---
# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>DataMarket Recommendations API から Azure Cognitive Services Recommendations API への移行
この記事では、[Microsoft DataMarket Recommendations API](https://datamarket.azure.com/dataset/amla/recommendations) から [Microsoft Azure Cognitive Services Recommendations API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) に移行する方法について説明します。

DataMarket Recommendations API は、2016 年 12 月 31 日に新規顧客の受け入れを停止し、2017 年 2 月 28 日に廃止される予定です。

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>Azure Cognitive Services Recommendations API の使用を開始するにはどうすればよいですか
Cognitive Services Recommendations API に移行するには、次の手順を実行します。

1. Azure サブスクリプションをまだお持ちでない場合、Azure サブスクリプションに [サインアップ](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) します。 
2. Cognitive Services Recommendations API にサインアップしてプログラムで使用するために、 [クイック スタート ガイド](cognitive-services-recommendations-quick-start.md) で詳細な手順を確認します。 
3. サービスの詳細を確認し、ドキュメントを検索するために、[Cognitive Services Recommendations API のランディング ページ](https://www.microsoft.com/cognitive-services/en-us/recommendations-api)に移動します。

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>モデルの構築に Recommendations UI を使用しましたが、 Cognitive Services Recommendations API に同様のツールはありますか
もちろんあります。 新しい [Recommendations UI](http://recommendations-portal.azurewebsites.net/) の URL は、http://recommendations-portal.azurewebsites.net です。 

> [!NOTE]
> DataMarket の資格情報は、ここでは機能しません。 Azure Portal でサブスクリプションにサインアップして、新しい [Recommendations UI](http://recommendations-portal.azurewebsites.net/)の使用に必要なアカウント キーを取得してください。
> アカウント キーをお持ちでない場合は、 [クイック スタート ガイド](cognitive-services-recommendations-quick-start.md)のタスク 1 を参照してください。
> 
> 

## <a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>新しい API の形式は DataMarket Recommendations API の形式と同じですか
新しい API では、DataMarket バージョンでサポートされているのと同じシナリオとプロセス フローがサポートされています。ただし、実際の API インターフェイスは [Microsoft REST API ガイドライン](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)に準拠するように更新されています。 一貫性が向上しているほか、Swagger をサポートするツールとの連携が強化されています。

各 API を確認するには、 [API エクスプローラー](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db)をお使いください。
*[試してみる]* ボタンを使用して API 呼び出しをテストします。 Recommendations API で使用されるファイル (カタログ ファイルと使用状況ファイル) の形式は変更されていないため、同じファイルやこれらのファイルの生成用に構築したインフラストラクチャを引き続き使用できます。

## <a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>Cognitive Services Recommendations API の新機能にはどのようなものがありますか
過去&2; か月間にわたって、Microsoft は Cognitive Services Recommendations API の新機能をリリースしてきました。

* コードの記述が不要な、トレーニングとモデルのテスト用の Recommendations UI
* 一度に数千もの推奨を提供するバッチ スコアリング
* 推奨モデルの品質を照会するビルド メトリックスのサポート
* ビジネス ルールのサポート
* 使用状況ファイルとカタログ ファイルを列挙してダウンロードする機能
* 推奨モデル内にある項目の特徴の品質を照会するための順位付けのビルドのサポート
* カタログ内の商品を検索する機能の追加

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>DataMarket Recommendations API のサポートはいつ終了しますか
[DataMarket の Recommendations API](https://datamarket.azure.com/dataset/amla/recommendations) は、2016 年 12 月 31 日に新規顧客の受け入れを停止し、2017 年 2 月 28 日には完全に廃止される予定です。 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>Cognitive Services Recommendations API でモデルを作成し直す必要があるデータがない場合はどうなりますか
移行が可能な限り簡単になるように、 DataMarket アカウントから新しい Azure Cognitive Services Recommendations API サブスクリプションへの、古いモデルの移動をサポートいたします。 [mlapi@microsoft.com](mailto://mlapi@microsoft.com) までお問い合わせください。 モデルを新しいアカウントに関連付ける前に、DataMarket サブスクリプション ID と Cognitive Services サブスクリプション ID をお知らせください。




<!--HONumber=Dec16_HO2-->


