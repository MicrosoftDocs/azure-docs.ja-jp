---
title: Custom Decision Service とは
titlesuffix: Azure Cognitive Services
description: この記事では、Custom Decision Service の概要について説明します。
services: cognitive-services
author: alekh
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: f4a0f519fe83162e7e161c3fda2b78a94b1d4bd1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863127"
---
# <a name="what-is-custom-decision-service"></a>Custom Decision Service とは

一般的な Web アプリやモバイル アプリケーションでは、フロント ページが複数の記事やその他の種類のコンテンツにリンクされています。 フロント ページが読み込まれると、そのフロント ページに含まれている記事をランク付けするように Custom Decision Service に要求できます。 したがって、ユーザーがクリックして記事を選択したときに、2 番目の要求を Custom Decision Service に送信でき、そのユーザーの意思決定の結果がログに記録されます。

Custom Decision Service は簡単に使用できます。必要なのは、コンテンツと数行の JavaScript の RSS フィードをアプリケーションに追加するだけです。

Custom Decision Service は、機械学習用の機能にコンテンツを変換します。 システムではこれらの機能を使用して、テキスト、イメージ、ビデオ、全体的なセンチメントの観点から、コンテンツを理解します。 [Entity Linking](../entitylinking/home.md)、[Text Analytics](../text-analytics/overview.md)、[Emotion](../emotion/home.md)、[Computer Vision](../computer-vision/home.md) など、他のいくつかの [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) を使用します。

Custom Decision Service の一般的ないくつかのユース ケースを以下に示します。

* ニュース Web サイトの記事のパーソナライズ
* メディア ポータルのビデオ コンテンツのパーソナライズ
* 広告の配置や広告から移動する Web ページの最適化
* ショッピング Web サイトのおすすめアイテムのランク付け。

Custom Decision Service は、現在、*無料のパブリック プレビュー*段階です。 Web サイトやアプリで記事のリストをパーソナライズすることができます。 特徴抽出は英語のコンテンツに最適です。 スペイン語、フランス語、ドイツ語、ポルトガル語、日本語などの他の言語には、[限られた機能](../text-analytics/overview.md)が提供されます。 このドキュメントは、新しい機能が利用可能になったときに改訂されます。

Custom Decision Service は、コンテンツのパーソナライズ ドメインにないアプリケーションで使用できます。 これらのアプリケーションは、カスタム プレビューに適している場合があります。 詳細については、[こちらからお問い合わせください](https://azure.microsoft.com/overview/sales-number/)。

## <a name="api-usage-modes"></a>API の使用モード

Custom Decision Service は、Web ページとモバイル アプリの両方に適用できます。 API は、ブラウザーまたはアプリから呼び出すことができます。 両方の API の使用方法は似ていますが、詳細がいくつか異なります。

## <a name="glossary-of-terms"></a>用語集

このドキュメントでよく使用される用語をいくつか以下に示します。

* **アクション セット**: Custom Decision Service でランク付けするコンテンツ アイテムのセット。 この設定は、*RSS* または *Atom* エンドポイントとして指定することができます。
* **ランク付け**: Custom Decision Service への各要求では、1 つ以上のアクション セットを指定します。 システムは、これらのセットからコンテンツ オプションをすべて選択することで応答し、ランク順に返します。
* **コールバック関数**: ユーザーが指定するこの関数は、UI のコンテンツをレンダリングします。 コンテンツは、Custom Decision Service から返されたランク順に並べられます。
* **報酬**: レンダリングされたコンテンツにユーザーがどれだけ応答したかを示す指標。 Custom Decision Service では、クリック数を使用してユーザーの応答を測定します。 クリック数は、アプリケーションに挿入されたカスタム コードを使用して、システムに報告されます。

## <a name="next-steps"></a>次の手順

* Custom Decision Service に[アプリケーションを登録する](custom-decision-service-get-started-register.md)
* [Web ページ](custom-decision-service-get-started-browser.md)や[スマートフォン アプリ](custom-decision-service-get-started-app.md)の最適化を開始します。
* 提供される機能の詳細については、[API リファレンス](custom-decision-service-api-reference.md)を参照してください。
