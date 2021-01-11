---
title: Form Recognizer とは
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Form Recognizer を使用すると、フォーム ドキュメントからキーと値のペアやテーブル データを識別して抽出できます。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 0df61c2ee42d468562efd67a2a66a90a5e4fda53
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723565"
---
# <a name="what-is-form-recognizer"></a>Form Recognizer とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer は、機械学習テクノロジを使用して、フォーム ドキュメントからテキストや、キーと値のペア、テーブル データを識別して抽出する Cognitive Services です。 フォームからテキストを取り込んで、元のファイル内の関係を含む構造化データを出力します。 手作業による操作やデータ サイエンスに関する専門知識があまりなくても、特定のコンテンツに合わせた正確な結果がすばやく得られます。 Form Recognizer は、カスタム モデル、あらかじめ構築されたレシート モデル、Layout API から成ります。 REST API を使用して Form Recognizer モデルを呼び出すことにより、複雑さを軽減し、自分のワークフローやアプリケーションに統合することができます。

Form Recognizer は、次のサービスで構成されています。
* **カスタム モデル** - フォームからキーと値のペアおよびテーブル データを抽出します。 これらのモデルは自分が用意した独自のデータでトレーニングされるため、実際のフォームに合わせて調整されます。
* **事前構築済みモデル** - 事前構築済みモデルを使用して、一意のフォームの種類からデータを抽出します。 現在利用可能なのは、英語のレシートと名刺用の事前構築済みモデルです。
* **Layout API** - テキストおよびテーブルの構造を、対応する境界ボックスの座標と共にドキュメントから抽出します。

<!-- add diagram -->

## <a name="custom-models"></a>カスタム モデル

Form Recognizer のカスタム モデルでは、独自のデータでトレーニングを行います。また、5 つのサンプル入力フォームだけで開始できます。 トレーニング済みのモデルは、元のフォーム ドキュメント内の関係を含む構造化データを出力できます。 モデルをトレーニングした後、モデルをテストおよび再トレーニングでき、最終的にはモデルを使用して、ニーズに従ってより多くのフォームから正確にデータを抽出できます。

カスタム モデルをトレーニングする際は、ラベル付けされたデータを使用したトレーニングとラベル付けされたデータを使用しないトレーニングのどちらかを選択できます。

### <a name="train-without-labels"></a>ラベルを使用しないトレーニング

Form Recognizer は、既定では教師なし学習を使用して、フォーム内のレイアウトを解釈し、フィールドとエントリ間の関係を解釈します。 入力フォームを送信すると、アルゴリズムによって種類別にフォームが分類されて、存在するキーとテーブルが検出されます。また、値がキーに、エントリがテーブルに関連付けられます。 手動によるデータのラベル付けは不要であり、大量のコーディングやメンテナンスも必要ありません。最初は、この方法を試すようお勧めします。

### <a name="train-with-labels"></a>ラベルを使用したトレーニング

ラベル付けされたデータによるモデルのトレーニングでは、ラベル付けされた指定のフォームを使用して教師あり学習を行うことで、目的とする値が抽出されます。 こちらの方が、複雑なフォームや、キーのない値を含んだフォームでも機能する性能のよいモデルが得られます。

Form Recognizer は、印刷されたテキストや手書きのテキストの要素について、[Layout API](#layout-api) を使用して予想されるサイズや位置を学習します。 その後、ユーザーによって指定されたラベルを使用して、ドキュメントに含まれるキーと値の関係を学習します。 新しいモデルをトレーニングする際はまず、手動でラベル付けされた同じタイプのフォーム 5 つを使用し、そのうえで、モデルの精度を改善するために必要であれば、ラベル付けされたデータを追加することをお勧めします。

## <a name="prebuilt-models"></a>事前構築済みのモデル

Form Recognizer には、一意のフォームの種類に対する事前構築済みモデルも含まれています。
### <a name="prebuilt-receipt-model"></a>事前構築済みのレシート モデル
事前構築済みのレシート モデルは、オーストラリア、カナダ、英国、インド、および米国のレストランやガソリン スタンド、小売店などで使用されている種類の英語のレシートを読み取るために使用されます。 このモデルでは、取引日時、販売店情報、税額、明細項目、合計金額などの主要な情報が抽出されます。 さらに、あらかじめ構築されたレシート モデルは、レシート内のすべてのテキストを認識して返すようにトレーニングされています。 

![サンプルのレシート](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>事前構築済みの名刺モデル
名刺モデルを使用すると、個人の名前、役職、住所、電子メール、会社、電話番号などの情報を英語の名刺から抽出できます。 

![サンプルの名刺](./media/business-card-english.jpg)

## <a name="layout-api"></a>Layout API

Form Recognizer は、高精細の光学式文字認識 (OCR) を使用して、テキストやテーブル構造 (テキストが関連付けられた行番号と列番号) を抽出することもできます。

## <a name="get-started"></a>はじめに

クイックスタートに従って、フォームからのデータの抽出を開始します。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

* [クライアント ライブラリのクイックスタート](./quickstarts/client-library.md) (すべての言語、複数のシナリオ)
* Web UI クイックスタート
  * [ラベルを使用したトレーニング - サンプル ラベル付けツール](quickstarts/label-tool.md)
* REST クイック スタート
  * カスタム モデルをトレーニングしてフォーム データを抽出する
    * [ラベルを使用しないトレーニング - cURL](quickstarts/curl-train-extract.md)
    * [ラベルを使用しないトレーニング - Python](quickstarts/python-train-extract.md)
    * [ラベルを使用したトレーニング - Python](quickstarts/python-labeled-data.md)
  * 米国のレシートからデータを抽出する
    * [レシートのデータを抽出する - cURL](quickstarts/curl-receipts.md)
    * [レシートのデータを抽出する - Python](quickstarts/python-receipts.md)
  * テキストとテーブル構造をフォームから抽出する
    * [レイアウト データを抽出する - Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>REST API を確認します

次の API を使用して、モデルのトレーニングを行い、フォームから構造化データを抽出します。

|名前 |説明 |
|---|---|
| **Train Custom Model**| 同じ種類の 5 つのフォームを使用して、フォームを分析する新しいモデルをトレーニングします。 手動でラベル付けしたデータを使ってトレーニングを行うには、_useLabelFile_ パラメーターを `true` に設定します。 |
| **Analyze Form** |ストリームとして渡された単一のドキュメントを分析し、カスタム モデルを使用してフォームからテキスト、キーと値のペア、テーブルを抽出します。  |
| **Analyze Receipt** |単一のレシート ドキュメントを分析し、レシート内の主要な情報とその他のテキストを抽出します。|
| **Analyze Layout** |フォームのレイアウトを分析してテキストとテーブル構造を抽出します。|

詳しくは、[REST API のリファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)をご覧ください。 以前のバージョンの API をご利用の方は、「[新機能](./whats-new.md)」の記事で、レシートに関する最新の変更点をご確認ください。

## <a name="input-requirements"></a>入力の要件
### <a name="custom-model"></a>カスタム モデル

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt"></a>事前構築済み

レシート モデルの入力要件は少し異なります。

* 形式は、JPEG、PNG、PDF (テキストまたはスキャン済み)、TIFF のいずれかである必要があります。
* ファイル サイズは 20 MB 未満である必要があります。
* 画像の寸法は、50 x 50 ピクセルから 10,000 x 10,000 ピクセルの間である必要があります。
* PDF の寸法は、最大で 17 x 17 インチにする必要があります (リーガル サイズまたは A3 サイズ以下の用紙に対応します)。
* PDF および TIFF の場合、最初の 200 ページのみが処理されます (Free レベルのサブスクリプションでは、最初の 2 ページのみが処理されます)。

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Form Recognizer サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

[クイック スタート](quickstarts/curl-train-extract.md)をすべて終え、[Form Recognizer API シリーズ](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)の使用を開始します。